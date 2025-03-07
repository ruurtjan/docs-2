---
title: "Studio Pro Overview"
category: "App Modeling"
description: "Describes Studio Pro in general: tabs, menus, shortcut keys."
menu_order: 10
tags: ["Studio Pro"]
aliases:
    - /refguide/desktop-modeler-overview.html
#If moving or renaming this doc file, implement a temporary redirect and let the respective team know they should update the URL in the product. See Mapping to Products for more details.
#This document is mapped to the landing page, update the link there if renaming or moving the doc file.
---

## 1 Introduction

Mendix Studio Pro is a tool for creating, viewing, and editing your Mendix applications.

One Studio Pro instance can have only one app open at a time, but you can open two Studio Pro instances when necessary.

{{% alert type="info" %}}
When Studio Pro is open, press <kbd>F1</kbd> to quickly jump to documentation.
{{% /alert %}}

This document describes the user interface of Mendix Studio Pro:

<img src="attachments/studio-pro-overview/studio-pro-diagram.png" alt="Studio Pro Diagram"  />

## 2 Top Bar

The Studio Pro top bar contains the following items:

* [Switch-to menu](#switch-to)
* [Menus](#menus) 
* [Buttons to run and view your app](#run-and-view)
* [Links to the Developer Portal and Marketplace](#links) 

### 2.1 Switch-to Menu {#switch-to}

The **Switch-to** menu is located at the top-left of Studio Pro and contains links to various parts of the platform. For more information on the **Switch-to** menu, see the [Navigation]() section in the *Developer Portal Guide*.

### 2.2 Menus {#menus}

In the Studio Pro top bar, you can see several menus, such as [Edit](edit-menu), [View](view-menu), and [Version Control](version-control-menu). Each menu contains menu items that allow you to perform various actions, for example, to [create a deployment package](create-deployment-package-dialog), set [preferences](preferences-dialog), or view the [Errors](errors-pane) pane. 

For more information on menus, see [Menus](menus). 

### 2.2 Run & View App {#run-and-view}

You can deploy your app by clicking the **Publish** or play (**Run locally**) buttons. To view your deployed app, click **View App**. 

For more information on deployment in Mendix, see [Deployment](/developerportal/deploy).

For more information on deploying your app and versioning it, see  the [Versioning an App Deployed to the Cloud](using-version-control-in-studio-pro#versioning-project) section in *Using Version Control in Studio Pro*. 

### 2.3 Links and User Profile Menu {#links}

You can find links to the [Mendix Studio](/studio) (if enabled), [Developer Portal](/developerportal), and [Marketplace](/appstore) in the top-right corner of Studio Pro.

Your profile picture is displayed next to them if you are signed in. When you click the profile picture, the drop-down menu is displayed with your full name and email, as well as links to your user profile, My Apps screen, and signing out option.  

## 3 App Structure

An app consists of individual files (*documents*) and settings that are grouped in folders and [modules](modules). The complete structure of your app can be viewed in the [App Explorer](project-explorer). 

## 4 Working Area

A working area is a current document tab that you work in. The working area and its settings differs depending on an editor (for example, pages, microflows, domain model editors) and type of the document.  

### 4.1 Document Tabs {#documents}

The documents you view and edit are shown in tabs. 

{{% alert type="info" %}}

This section describes documents in the working area, not panes that you can open and position around the working area. For more information on behavior of panes, see the [Layout of Panes](view-menu#layout-of-panes) section in *View Menu*.

{{% /alert %}}

You can have a number of tabs open, just like in a modern web browser. They can be closed, reordered, and shown side by side. 

Each document has its own save state, history, and future, so undo and redo actions are unlimited.

## 5 Dockable Panes

Dockable panes can be positioned around the working area and contain various elements and settings. For example, you can view list of errors, configure properties of a specific document or an element, view toolbox. Panes are opened via [Menus](menus).

## 6 Status Bar

At the bottom of the Studio Pro main window pane is a status bar.

On the left is the current status of Studio Pro.

On the right is the currently selected language. If you have set up multiple languages in your app you can change the currently selected language by clicking here. For more information see [Language Menu](translatable-texts).

## 7 Shortcut Keys

### 7.1 General Shortcut Keys

In the main window pane, <kbd>Ctrl</kbd> + <kbd>Tab</kbd> can be used to navigate between all open documents. Other shortcut keys that can be used in the main window pane can be found next to their relevant menu item in  [Menus](menus).

In most dialog boxes for editing properties, the following shortcut keys can be used:

| Key | Description |
| --- | --- |
| <kbd>Ctrl</kbd> + <kbd>Enter</kbd> | Confirms all changes and closes the dialog box. Works as if the **OK** button was clicked. This is especially useful if the focus is on a multi-line text box; otherwise, pressing <kbd>Enter</kbd> will have the same effect. |
| <kbd>Esc</kbd> | Cancels all changes and closes the dialog box. Works as if the **Cancel** button was clicked. |
| <kbd>Ctrl</kbd> + Mouse scroll wheel | Zooms in or out. This works in all editors. |
| <kbd>Shift</kbd> + Mouse scroll wheel | Scrolls left or right. Works as if you were using the horizontal scroll bars. |

In most edit grids (such as the list of attributes in the entity properties), the following shortcut keys can be used:

| Key | Description |
| --- | --- |
| <kbd>Ctrl</kbd> + <kbd>N</kbd> | Creates a new item. |
| <kbd>Enter</kbd> | Edits the currently selected item. |
| <kbd>Delete</kbd> | Deletes the currently selected item(s). |

### 7.2 Domain Model Editor Shortcut Keys

The following shortcut keys are available in the domain model editor:

| Key | Description |
| --- | --- |
| <kbd>F2</kbd> | Edits the name of the selected element. |
| <kbd>Ctrl</kbd> + <kbd>A</kbd> | Selects all entities. |
| <kbd>Esc</kbd> | Clears selection. |

### 7.3 Page Editor Shortcut Keys

The following shortcut keys are available in the page editor:

| Key | Description |
| --- | --- |
| Arrow keys | Moves the selection box to the widget or element in the direction of the arrow key. For example, if currently a label is selected and you press the right arrow key, the text box to the right of it becomes selected. |
| <kbd>Ctrl</kbd> + arrow keys | Moves the currently selected item up/down or left/right. This works on table columns and rows, tab pages, grid and data view buttons, search fields, etc. |
| <kbd>Enter</kbd> | Edits the properties of the currently selected object in a dialog box. |
| <kbd>F2</kbd> | Edits the caption of a label or button inline on the page. You can also just start typing a letter or a digit and the caption will be replaced by what you type. |

### 7.4 Microflow Editor Shortcut Keys

The following shortcut keys are available in the microflow editor:

| Key | Description |
| --- | --- |
| Arrow keys | Moves the selection box to the activity or element in the direction of the arrow key. For example, if currently a show page activity is selected and you press the right arrow key, the end event to the right of it becomes selected. |
| <kbd>Ctrl</kbd> + arrow keys | Moves the currently selected item up/down or left/right. |
| <kbd>Enter</kbd> | Edits the properties of the currently selected object in a dialog box. |
| <kbd>F2</kbd> | Edits the name of the return value of the currently selected activity. This shortcut will not function on activities that do not return a result. |
| <kbd>Home</kbd> | Highlights and focus on the start event of the current microflow. |
| <kbd>End</kbd> | Highlights and focus on an end event in the current microflow. If there are multiple end events, clicking **End** multiple times will toggle between the different events. |
| <kbd>Shift</kbd> when resizing an activity | When resizing the entity, by holding <kbd>Shift</kbd> , the microflow component will stay centered at its current position and will expand equally in all directions. |
| <kbd>Ctrl</kbd> when selecting multiple activities | When pressing the <kbd>Ctrl</kbd>, you can select additional microflow components. Clicking a selected component while holding <kbd>Ctrl</kbd> will deselect it. |

### 7.5 Microflow Debugger Shortcut Keys {#debugger-shortcuts}

The following shortcut keys are available for the microflow debugger:

| Key | Description |
| --- | --- |
| <kbd>Alt</kbd> + <kbd>F5</kbd> | *Step into* – moves the debugger into the sub-microflow or loop.  |
| <kbd>Alt</kbd> + <kbd>F6</kbd> | *Step over* – moves the debugger to the next step in the same microflow. |
| <kbd>Alt</kbd> + <kbd>F7</kbd> | *Step out* – instructs the debugger to leave the sub-microflow or loop.  |
| <kbd>Alt</kbd> + <kbd>F8</kbd> | *Continue* – instructs the debugger to continue until it reaches another breakpoint. |

### 7.6 Language Shortcut Keys

The following shortcut keys control the currently selected language:

| Key | Description |
| --- | --- |
| <kbd>Ctrl</kbd> + <kbd>L</kbd> | Choose the next language in the list of languages chosen in **Language Settings…**. |
| <kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>L</kbd> | Choose the previous language in the list of languages chosen in **Language Settings…**. |

## 8 Read More

* [App Explorer](project-explorer)
* [Menus](menus)

  
