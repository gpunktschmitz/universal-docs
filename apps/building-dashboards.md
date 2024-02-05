---
description: Apps are the root component for your web page.
---

# Apps

Apps can contain one or more pages. The simplest app will contain a single page with some content. You can call any PowerShell cmdlet that is available on your machine to populate your app.

## Designer

You can use the App page designer to create new pages for your application. After creating a new app, click the New Page button on the right hand side.&#x20;

<figure><img src="../.gitbook/assets/image (114).png" alt=""><figcaption><p>New Page Button</p></figcaption></figure>

In the modal for the new page, ensure that you leave the Use Designer setting enabled.&#x20;

<figure><img src="../.gitbook/assets/image (409).png" alt=""><figcaption><p>Use Designer</p></figcaption></figure>

Once created, you will be taken to the designer for the page.&#x20;

### Layout

The designer allows for dragging and resizing of components that have been added to the page. Click the Add Component button to add a new component. Once clicked, you will be prompted with a list of supported components that will work in forms.&#x20;

### Component Properties

Component properties can be set in the Component Properties section of the left-hand drawer. The input controls will change based on the component type. You will see the component update immediately after changing the property. PowerShell script is running on the server to update these components.

#### Event Handlers

Event handlers provide a special code editor that will open in a bottom drawer. Click the edit button for the event handler to edit the script that will execute when the event takes place.&#x20;

<figure><img src="../.gitbook/assets/image (447).png" alt=""><figcaption><p>Event Handler Edit Button</p></figcaption></figure>

The event handler editor supports code completion and integrates with PSScriptAnalyzer to provide code analysis.&#x20;

<figure><img src="../.gitbook/assets/image (396).png" alt=""><figcaption><p>Event Handler Editor</p></figcaption></figure>

After changing the code, you can simply click away from the drawer. The code will be persisted.&#x20;

### Saving

While making changes in the designer, you will want to periodically save changes. This will persist the app page back to the server. Changes made before clicking save are only available within your browser.&#x20;

The save button is located on the top toolbar and will reload the page after save.&#x20;

### Code Generation

The designer creates PowerShell scripts based on the components in the page. You can view the current code for the page by clicking the Code View button. A drawer will appear with the current source for the page.&#x20;

Here's an example of script generated by the designer.&#x20;

```powershell
New-UDPage -Name 'MyPage' -Content {
    New-UDButton -Id 'myButton' -OnClick {
        Show-UDToast "Hello!"
    }
} -Generated -Layout @{
    Large = @{}
}
```

Editing the generated code is discouraged because it can cause issues with loading components into the designer. If you wish to edit the pages directly, you will want to remove the `-Generated` parameter in `dashboards.ps1` to allow for access to the code-based apps.&#x20;

## Code

If you would like more control over how app pages are created, you can script the pages yourself. This allows for greater flexibility but does not enable the ability to edit the page in the designer.

Here's an example of simple dashboard that displays some text.

```powershell
New-UDApp -Title 'My New Dashboard' -Content {
    New-UDTypography -Text 'Hello!'
}
```

### New-UDApp

The top-level cmdlet for dashboards is `New-UDApp`. You need to call it when returning an app. You can use it with or without pages.&#x20;

#### Content

The content of the app is a series of components to display on the page. It's a script block that will return all the components in the order they will be rendered on the page. You can use the Grid component to layout items and display things like text with typography.&#x20;

```powershell
New-UDApp -Title 'My New Dashboard' -Content {
    New-UDTypography -Text 'Hello!'
}
```

#### Header Customization

You can customize the header of the app using several parameters.&#x20;

To change the navigation layout, use the `-Navigation` and `-NavigationLayout` parameters.

```powershell
New-UDApp -Content {
} -Navigation (
    New-UDList -Children {
        New-UDListItem -Label "Home"
        New-UDListItem -Label "Getting Started" -Children {
            New-UDListItem -Label "Installation" -OnClick {}
            New-UDListItem -Label "Usage" -OnClick {}
            New-UDListItem -Label "FAQs" -OnClick {}
            New-UDListItem -Label "System Requirements" -OnClick {}
            New-UDListItem -Label "Purchasing" -OnClick {}
        }
    }
) -NavigationLayout permanent
```

### Components

Components are the individual widgets that you can place on you app. There are components for displaying data, taking user input, adding text and images and more. Components can be downloaded as PowerShell modules and added to your app.

Components are be caused using the standard verb-name syntax for any PowerShell cmdlet.

```powershell
New-UDPage -Content {
    New-UDTextbox
}
```

Learn more about [components here](building-dashboards.md#components).

### Pages

You can specify multiple pages within an app. Each page defines a route. As for v3, all pages are dynamic. PowerShell will execute on each page load to render the new page. Since UD is a single page application, the web browser does not need to refresh the entire web page when navigating between the different app pages.

```powershell
$Pages = @()
$Pages += New-UDPage -Name 'My Home Page' -Content {}
$Pages += New-UDPage -Name 'Diagnostics' -Content {}
New-UDApp -Pages $Pages -Title 'Dashboard'
```

Learn more about [Pages here](building-dashboards.md#pages).&#x20;

### Built-in Variables

Built-in variables can be found on the [variables page](../platform/variables.md#dashboards).

### Debugging

{% hint style="info" %}
You can also use the [Debugging Tools](../development/debugging-scripts.md#integrated-debugger) with apps.
{% endhint %}

When building an app, you will likely run into issues with cmdlet calls or syntax. Apps will auto reload as you make changes to the app files. If an app fails to start, you can navigate to the admin page, click Apps and click the Info button next to your app.

The Log tab will show all the logging coming from the PowerShell execution from within in your app. This should allow you to easily see errors and warnings coming from your app.

You can use `Write-Debug` to add additional log messages to your app. To enable debug logging, you will have to set the `$DebugPreference` variable at the top of your app script.

```powershell
$DebugPreference = 'Continue'
```

### Menu

You can customize the appmenu by using the `-Menu` parameter.&#x20;

```powershell
New-UDApp -Title 'App' -Content {

} -Menu {
    New-UDMenuItem -Text 'Profile' -OnClick {
        Show-UDModal -Content {
            New-UDTypography -Text 'Welcome to your profile!'
        }
    }
}
```