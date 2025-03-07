---
title: "Offline-First"
category: "Mobile"
menu_order: 30
tags: ["offline", "native", "mobile", "studio pro"]
---

## 1 Introduction

Offline-first applications work regardless of the connection in order to provide a continuous experience. Pages and logic interact with an offline database on the device itself, and data is synchronized with the server. This results in a snappier UI, increased reliability, and improved device battery life.

{{% alert type="info" %}}
It is important to understand that offline-first is an architectural concept and not an approach based on the network state of the device. Offline-first apps do not rely on a connection, but they can use connections (for example, you can call microflows, use a Google Maps widget, or use push notifications).
{{% /alert %}}

Mendix supports building offline-first applications for [native mobile](native-mobile) and [hybrid mobile](hybrid-mobile) apps. Both native and hybrid apps share the same core, and this gives them the same offline-first capabilities. Native mobile apps are always offline-first, but for hybrid mobile apps, it depends on the navigation profile that is configured. The data is stored on the device in a local database, and the files are stored on the file storage of the device.

Mendix Studio Pro performs validations to make sure your app follows an offline-first approach and works even when there is no connection.

During development, the [Make It Native mobile app](https://play.google.com/store/apps/details?id=com.mendix.developerapp) (for native mobile apps) or [Mendix mobile app](https://play.google.com/store/apps/details?id=com.mendix.SprintrMobile) (for hybrid mobile apps) can be used to preview and test your Mendix app on a device. The first time your Mendix app is loaded, an internet connection will be required to create a session on the server and download the necessary data and resources. After this initial synchronization, data will remain available in the app even without an internet connection. Subsequent synchronizations will be performed when requested by the user, through application logic or after a model change.

## 2 Synchronization{#synchronization}

Mendix automatically analyzes your app's data model to determine which entities should be synchronized based on the pages and nanoflows used within your offline navigation profile. In addition, the platform takes entity access into account so that only the data the user is allowed to access is synchronized.

Synchronization is automatically triggered during the following scenarios:

* The initial startup of your mobile app
* The first startup of your mobile app after your Mendix app is redeployed when the following conditions are matched:
 * There is a network connection
 * You are using a new Mendix version or the domain model used in the offline-first app has changed
* After the app user logs in or out

Synchronization can also be configured via different places in your Mendix app, for example:

* As an action on a button
* As an action in a nanoflow
* As a pull-down action on a list view (for native mobile only)

Synchronization is performed on the database level. This means if you synchronize while having some uncommitted changes for an object, the attribute values in local database will be synchronized, ignoring the uncommitted changes. Uncommitted changes are still available after a synchronization.

### 2.1 Synchronization Types

You can perform synchronization on two levels:

* [Full synchronization](#full-sync)
* [Selective synchronization](#selective-sync)

#### 2.1.1 Full Synchronization {#full-sync}

This mode performs both the upload and the download phases for all entities used in the offline-first app. You can customize the behavior of each entity with [customizable synchronization](#customizable-synchronization).

#### 2.1.2 Selective Synchronization {#selective-sync}

Selective synchronization can only be done through a **Synchronize** action inside a nanoflow. In this mode, a specific set of objects will be synchronized. These objects can be either all unsynchronized objects when the [synchronize unsynchronized objects](synchronize#synchronize-unsynchronized-objects) mode is selected, or a manually selected set of objects when the [synchronize selected object(s)](synchronize#synchronize-selected-object-s) mode is selected.

Synchronization performed using a UI element (for example, a button or an on-change action) performs the full synchronization.

### 2.2 Synchronization Phases

The synchronization process consists of two phases. In the [upload phase](#upload), your app updates the server database with the new or changed objects that are committed. In the [download phase](#download), your app updates its local database using data from the server database.

#### 2.2.1 Upload Phase {#upload}

The upload phase begins with a referential integrity validation of the new or changed objects that should be committed to the server. This validation checks each to-be-committed object's references to other objects. If this validation fails, the synchronization is aborted and an error message is shown (if the error is not caught).

During [full synchronization](#full-sync) this validation ensures that all referenced objects are committed to the local database. If a referenced object is created on the device and not yet committed to the local database, synchronization is aborted to prevent an invalid reference value on the server database. Note that synchronization only works on the database level.

For example, when a committed `City` object refers to an uncommitted `Country` object, synchronizing the `City` object will yield an invalid `Country` object reference, which will break the app's data integrity. If a synchronization is triggered while data integrity is broken, the following error message will appear (indicating an error in the model to fix): **Sync has failed due to a modeling error. Your database contains objects that reference uncommitted objects: object of type `City` (reference `City_Country`)**. To fix this, such objects must also be committed before synchronizing (in this example, `Country` should be committed before synchronizing).

During [selective synchronization](#selective-sync), an additional referential integrity validation is performed to ensure that all referenced objects are at least synchronized once to the server database or included in the selection.

For example, synchronizing only a committed `City` object referencing an offline `Country` object (created on the device and committed to the local database but not yet synchronized) would break the integrity of the `City` object on the server database since the `Country` object is not stored in the server database. In this case a similar error message will appear, indicating that it is a modeling error. To fix this, such objects must be selected for synchronization. In this example, `Country` should either be selected with synchronization or synchronized before attempting to synchronize `City` object.

The upload phase executes the following operations after validation:

1. The local database can be modified only by committing an object. Such an object can be a new object created (while offline), or it can be an existing object previously synced from the server. The upload phase detects which objects have been committed to the local database since the last synchronization. This detection differs per synchronization type. For **Synchronize all**, all committed objects in the local database are selected. For **Synchronize objects**, all committed objects from the list of selected objects are selected.
2. <a name="steptwo"></a>If there are changed or new file objects, their contents are uploaded to the server and stored temporarily. Each file is uploaded in a separate network request.
3. <a name="stepthree"></a>All the changed and new objects are committed to the server, and the content of the files are linked to the objects. This step is performed in a single network request. Any configured before- or after-commit event handlers on these objects will run on the server as usual, after the data has been uploaded and before it is downloaded.

#### 2.2.2 Download Phase {#download}

If the upload phase was successful, the download phase starts in which the local database is updated with the newest data from the server database. The behavior of download phase differs per synchronization type.

**Full synchronization** — A network request is made to the server per entity to retrieve the newest data from the server database. You can manage which entities are synchronized to the local database by customizing your app's synchronization behavior. For more details on this procedure, see the [Customizable Synchronization](#customizable-synchronization) section below. The download process also downloads the file entities' contents and saves that to your device storage. This process is incremental. The app only downloads the contents of a file object if the file has not been downloaded before, or if the file has been changed since it was last downloaded. The changed date attribute of the file entity is used to determine if the contents of a file object have changed.

**Selective synchronization** — Only the objects selected for synchronization are synchronized to the local database. There are no extra network requests made to retrieve these objects. The objects are returned in the response of a network request made during the upload phase. If a file entity is selected for synchronization, its content is also updated on the device storage incrementally. The logic is the same in the full synchronization.

### 2.3 After Synchronization

After synchronization is completed, the widgets on your app's current page will be refreshed to reflect the latest data. If the synchronization is triggered from a nanoflow, all nanoflow object/list variables are updated (uncommitted changes are still preserved).

Please note that a nanoflow object variable's value might become `empty` after synchronization, if the object is removed from the device during synchronization. This might happen under the following cases:

* The object is deleted on the server
* The current user does not have enough access to the object (defined by the security access rules)
* The entity is configured with an XPath constraint on the [customizable synchronization](#customizable-synchronization) screen, and the object no longer matches the specified XPath constraint
* The entity is configured with **Nothing (clear data)** option on the customizable synchronization screen
* The upload phase fails for the object — for example when a before commit event handler returns false, or committing fails due to violation of a unique validation

### 2.4 Customizable Synchronization {#customizable-synchronization}

{{% alert type="warning" %}}
These settings are not applied for [selective synchronization](#selective-sync).
{{% /alert %}}

By default, Mendix automatically determines which objects need to be synchronized as mentioned in [Synchronization](#synchronization).

Depending on the use-case, more fine-grained synchronization controls might be required. Therefore, it is possible to change the download behavior for an entity. You can choose between the following options:

* **All Objects** — Download all objects applying the regular security constraints.
* **By XPath** — Only download the objects which match the [XPath Constraints](xpath-constraints) in addition to the regular security constraints. This means all previously synchronized objects that do not match the XPath constraint will be removed.
* **Nothing (clear data)** — Do not download any objects automatically, but do clear the data stored in the database for this entity when performing a synchronization (this can be useful in cases where the objects should only be uploaded, for example a `Feedback` entity).
* **Nothing (preserve data)** — Do not download any objects automatically, and do not clear the data stored in the database for this entity when performing a synchronization  (this can be useful in cases where you want have full control over the synchronization and should be used in combination with the [Synchronize to device](synchronize-to-device) or [Synchronize](synchronize) activity with specific objects selected).

If you have custom widgets or JavaScript actions which use an entity that cannot be detected by Studio Pro in your offline-first profile (because its only used in the code), you can use customizable synchronization to include such entities.

{{% image_container width="450" %}}![custom synchronization](attachments/offline-first/custom-sync.png){{% /image_container %}}

### 2.5 Limitations

Running multiple synchronization processes at the same time is not supported, regardless the of the type (**full** or **selective**). For more information, see the [Limitations](synchronize#limitations) section of the *Synchronize Reference Guide*.

### 2.6 Error Handling {#error-handling}

During synchronization, errors might occur. This section describes how Mendix handles these errors and how you can prevent them.

#### 2.6.1 Network-Related Errors {#network-errors}

Synchronization requires a connection to the server, so during synchronization, errors may occur due to failing or poor network connections. Network errors may involve a dropped connection or a timeout. By default, the timeout for synchronization is 30 seconds per network request for hybrid mobile apps. For native apps, there is no default timeout, and the timeout is determined by the platform and OS version.

The synchronization is atomic, which means that either everything or nothing is synchronized. Exceptions are described in the [Model- or Data-Related Errors](#othererrors) section below.

If a network error happens during the file upload (via [step 2 in the upload phase](#steptwo)), Mendix retries to upload the failed files. If there is an error for the second time, the synchronization is aborted. The changes at that moment are kept on the local device, so it can be retried later.

If a network error occurs while uploading the data (via [step 3 in the upload phase](#stepthree)), the data is kept on the local device and no changes are made on the server. Any files uploaded in [step 2](#steptwo) will be uploaded again during the next synchronization.

If a network error (such as a timeout) occurs after uploading the data (at [step 3 in the upload phase](#stepthree)), the data is kept on the local device. However, since the server has already started working on the request it will complete the request and commit the changes to server database. The device cannot distinguish whether the server processed the request or not, so the next synchronization attempt will contain the already-applied changes. In this case, the server will behave differently based on Mendix version. In Mendix Studio Pro v8.18 or below, the server will commit the same changes again, which might overwrite potential changes made by other users between the two synchronizations. From Studio Pro v8.18 and above this process is optimized and the server will not commit the same changes because they have been applied before.

If a network error occurs during the download phase, no data is updated on the device. Therefore the user can keep working or retry. The effects of the upload phase are not rolled back on the server.

If the synchronization is called from a nanoflow, the error can be handled using nanoflow error handling. In other cases (for example, if synchronization is called from a button or at startup), a message will be displayed to the user that the data could not be synchronized.

#### 2.6.2 Model- or Data-Related Errors {#othererrors}

During the synchronization, changed and new objects are committed. An object's synchronization might encounter problems due to the following reasons:

* The object is no longer available on the server (either from deletion or inaccessibility due to access rules)
* A member of the object has become inaccessible due to access rules
* An error occurs during the execution of a before- or after-commit event microflow
* The object is not valid according to domain-level validation rules

{{% alert type="warning" %}}When a synchronization error occurs because of one the reasons above, an object's commit is skipped, its changes are ignored, and references from other objects to it become invalid. Objects referencing such a skipped object (which are not triggering errors) will be synchronized normally. Such a situation is likely to be a modeling error and is logged on the server. To prevent data loss, the attribute values for such objects are stored in the `System.SynchronizationError` entity (since Mendix 8.12).  {{% /alert %}}

### 2.6.3 Preventing Synchronization Issues {#prevent-sync-issues}

To avoid the problems mentioned above, we suggest following these best practices:

* Do not remove, rename, or change the type of entities or their attributes in offline apps after your initial release — this may cause objects or values to be no longer accessible to offline users (if needed, you can do an "in-between" release that is still backwards-compatible, and then make the changes in the next release after all the apps are synchronized)
* Do not delete objects which can be synced to offline users (this will result in lost changes on those objects when attempted to synchronize them)
* Avoid using domain-level validation for offline entities – use nanoflows or input validation instead (it is also a good practice to validate again on the server using microflows)
* When committing objects that are being referenced by other objects, make sure the other objects are also committed

If synchronization is triggered using a synchronize action in a nanoflow and an error occurs, it is possible to handle the error gracefully using the nanoflow error handling.

### 2.6.4 Conflict Resolution {#conflict-res}

It can happen that multiple users synchronize the same state of an object on their device, change it, and then synchronize this object back to the server. In this case, the last synchronization overwrites the entire content of the object on the server. This is also called a "last wins" approach.

If another approach is needed, conflicts can be detected in a before-commit microflow (for example, by using a revision ID attribute on the entity). Based on that, custom conflict resolution can be performed.

## 3 Best Practices {#best-practices}

To ensure the best user experience for your Mendix application, follow these best practices:

* Limit the amount of data that will be synchronized by customizing the synchronization configuration or security access rules
* Because network connections can be slow and unreliable and mobile devices often have limited storage, avoid synchronizing large files or images (for example, by limiting the size of photos)
* Try to synchronize through a nanoflow instead of a UI element so you can add error handling to the synchronization activity which can handle cases when synchronization fails (connection errors, model and data related errors, and more)
* Synchronize large files or images using selective synchronization
* Use an `isDeleted` Boolean attribute for delete functionality so that conflicts can be handled correctly on the server
* Use before- and after-commit microflows to pre- or post-process data
* Use a [microflow call](microflow-call) in your nanoflows to perform additional server-side logic such as retrieving data from a REST service, or accessing and using complex logic such as Java actions
* Help your user remember to synchronize their data so it is processed as soon as possible: you can check for connectivity and automatically synchronize in the nanoflow that commits your object, or remind a user to synchronize while using a notification or before signing out to ensure no data is lost

## 4 Ensuring Your App Is Offline-First {#limitations}

Mendix helps developers build rich offline-first apps. However, there are some limitations. See the subsections below for details.

### 4.1 Microflows {#microflows}

Microflows can be called from offline apps by using [microflow call](microflow-call) action in your nanoflows to perform logic on the server. However, it works a bit different from when used in online profiles, these differences are explained below:

#### 4.1.1 Microflow Arguments Type

* Passing an object or a list of a persistable entity is not supported
* Passing an object or a list of a non-persistable entity that has an association with a persistable entity is not supported (such an association can be an indirect association)
* Passing a non-persistable entity that was created in another microflow is not supported

#### 4.1.2 UI Actions

UI-related actions will be ignored and will not have any effect. We encourage you to model such UI-side effects in the caller nanoflow.

These actions are as the following:

* [Show message](show-message)
* [Show validation message](validation-feedback)
* [Show home page](show-home-page)
* [Show page](show-page)
* [Close page](close-page)
* [Download file](download-file)

#### 4.1.3 Object Side-Effects

Changes to persistable objects made in a microflow will not be reflected on the client unless you synchronize. Non-persistable objects must be returned in order for changes to be reflected.

#### 4.1.4 Microflow Return Value

* Returning an object or a list of persistable entity is not supported
* Returning an object or a list of a non-persistable entity that has an association with a persistable entity is not supported (such association can be an indirect association)

#### 4.1.5 Language Switching

To be able to switch the language of a Mendix app, a device must be online and have access to the Mendix runtime. For more information on the runtime, see the [Runtime Reference Guide](runtime).

### 4.2 Offline Microflow Best Practices {#offline-mf-best-practices}

To make microflow calls work from offline-first apps, Mendix stores some microflow information in the offline app. That information is called from the app. This means that changes to microflows used from offline apps must be backwards-compatible, because there can be older apps which have not received an over the air update yet. All microflow calls from such a device will still contain the old microflow call configuration in nanoflows, which means that the request might fail. For more information on over the air updates, see [How to Release Over the Air Updates with App Center's CodePush](/howto/mobile/how-to-ota).

To avoid backwards-compatibility errors in offline microflow calls after the initial release, we suggest these best practices:

* Do not rename microflows or move them to different modules
* Do not rename modules that contain microflows called from offline apps
* Do not add, remove, rename, or change types of microflow parameters
* Do not change return types
* Do not delete a microflow before making sure that all devices have received an update

If you want to deviate from the practices outlined above, introduce a new microflow. You can change the contents of the microflow, but keep in mind that older apps might call the new version of the microflow until they are updated.

### 4.3 Autonumbers & Calculated Attributes {#autonumbers}

Both autonumbers and calculated attributes require input from the server; therefore, they are not allowed. Objects with these attribute types can still be viewed and created offline, but the attributes themselves cannot be displayed.

### 4.4 Default Attribute Values {#default-attributive}

Default attribute values for entities in the domain model do not have any effect on objects created offline. Boolean attributes will always default to `false`, numeric attributes to `0`, and other attributes to `empty`.

### 4.5 Many-to-Many Associations {#many-to-many}

Many-to-many associations are not supported. A common alternative is to introduce a third entity that has one-to-many associations with the other entities.

### 4.6 Inheritance {#inheritance}

It is not possible to use more than one entity from a generalization or specialization relation. For example if you have an `Animal` entity and a `Dog` specialization, you can use either use `Animal` or `Dog`, but not both from your offline profile. An alternative pattern is to use composition (for example, object associations).

### 4.7 System Members {#system-members}

System members (`createdDate`, `changedDate`, `owner`, `changedBy`) are not supported.

### 4.8 Excel and CSV Export {#excel-cv}

Excel and CSV export are not available in offline applications.
