---
title: UI Config Files
description: Customizing the UI in Vendr, the eCommerce solution for Umbraco
---

With Vendr, there are minimal rules about what information you are required to record about an Order, however, this does pose a problem for how we provide a User Interface for managing carts and orders when we don't know exactly what properties you are going to be recording.

In order to allow this flexibility whilst still providing an ability to view and manage carts and orders in the back-office, Vendr supports a number of UI config files to map [Order/Order Line Properties](../properties/) to it's various UI elements.

## Supported UI Config Files

The configuration files supported by Vendr are.

* **cart.list.config.js** - Cart list view configuration.
* **cart.editor.config.js** - Cart editor view configuration.
* **order.list.config.js** - Order list view configuration.
* **order.editor.config.js** - Order editor view configuration.

<message-box type="info" heading="Fallbacks">

If there are no cart config files defined, then Vendr will fallback tot he order config files.

</message-box>

## Assigning a UI Config File to a Store

To assign a UI config file to a Store, this is done by file name convention where configs are looked for in `App_Plugins/Vendr/config` with the following file name format `{storeAlias}.{entityType}.{viewType}.config.js`. If no store specific file is found, it will fallback to the default `{entityType}.{viewType}.config.js`.

## Cart/Order List Config Files

With these configuration file, you can customise the columns displayed in the Cart/Order list view.

### Example Cart/Order List Config File

````javascript
{
    properties: [
        { alias: "color", header: "Color", placeholder: "Undefined" },
        { alias: "size", header: "Size", placeholder: "Undefined", align: "right" }
    ]
}
````

The following properties are supported.

| Name | Description |
| ---- | ----------- |
| `alias` | The alias of the Order property to use |
| `label` | A label to display as the table column header |
| `align` | Sets the alignment of the column. Can be `left` (default), `center` or `right` |
| `placeholder` | The placeholder value to display if there is now Order property value |
| `template` | An angular template to use for rendering the property value. Defaults to `{​{ properties['alias'].value }​}` |

Properties configured to display in the list view will appear in order, after the cart name column.

![Order List Properties](/media/screenshots/cart_list_properties.png)

## Cart/Order Editor Config Files

With these configuration files, you can completely customize the Cart/Order Editor interface to suit your particular needs.

### Example Cart/Order Editor Config File

An example of an Order Editor config file would look something like this:

````javascript
{
    orderLine: {
        properties: [
            { alias: "color", label: "Color", isReadOnly: true, showInOrderLineSummary: true },
            { alias: "size", label: "Size", isReadOnly: true }
        ]
    },
    customer: {
        // Firstname, Lastname and Email are already known
        company: { alias: "company", label: "Company Name" },
        taxCode: { alias: "taxCode", label: "Tax Code" },
        telephone: { alias: "telephone", label: "Telephone" },
    },
    billing: {
        addressLine1: { alias: "billingAddressLine1", label: "Street Address Line 1" },
        addressLine2: { alias: "billingAddressLine2", label: "Street Address Line 2" },
        city: { alias: "billingCity", label: "City" },
        zipCode: { alias: "billingZipCode", label: "Zip Code" },
        telephone: { alias: "billingTelephone", label: "Telephone" },
        // Country and Region are already known
    },
    shipping: {
        enabled: true,
        sameAsBilling: { alias: "shippingSameAsBilling", label: "Same as billing", trueValue: "1", falseValue: "0" },
        firstName: { alias: "shippingFirstName", label: "First Name" },
        lastName: { alias: "shippingLastName", label: "Last Name" },
        addressLine1: { alias: "shippingAddressLine1", label: "Street Address Line 1" },
        addressLine2: { alias: "shippingAddressLine2", label: "Street Address Line 2" },
        city: { alias: "shippingCity", label: "City" },
        zipCode: { alias: "shippingZipCode", label: "Zip Code" },
        telephone: { alias: "shippingTelephone", label: "Telephone" },
        // Country and Region are already known
    },
    notes: {
        customerNotes: { alias: "comments", label: "Customer Comments" },
        internalNotes: { alias: "notes", label: "Internal Notes" }
    },
    additionalInfo: [
        { alias: "ipAddress", label: "IP Address", isReadOnly: true }
    ]
}
````

The Cart/Order Editor config file is broken up into a series of sections, each of which relate to a particular section of the Cart/Order Editor User Interface.

### Order Line Config Options

The Order Line config block configures which Order Line properties should be viewable and/or manageable within the Cart/Order Editor UI. For each Order Line Property you can provide the following options:

| Name | Description |
| ---- | ----------- |
| `alias` | The alias of the Order Line property |
| `label` | A friendly label to display for this property in the editor interface |
| `description` | A friendly description to display for this property in the editor interface |
| `isReadOnly` | Sets the property as read only and so doesn't provide a means of editing the value in the editor interface (Default: `false`) |
| `showInOrderLineSummary` | Sets whether to display this property in the Order Lines summary next to the SKU in the Order editor interface (Default: `true`) |
| `view` | Sets the name or path of a Property Editor to use when editing this property |
| `config` | Defines a JSON config for the Property Editor if required |

Properties configured to display in the Order Line Summary will be displayed inline next to the Order Lines SKU as follows:

![Order Line Summary](/media/screenshots/order_line_summary.png)

Where there are editable Order Line Properties for an Order Line, a pencil icon is displayed next to the Order Lines Product name which when clicked will open out the Order Line Property editor interface for that Order Line.

![Order Line Property Editing](/media/screenshots/order_line_property_editing.png)

### Customer Config Options

The Customer config block configures which Cart/Order properties relate to an Cart/Orders customer information. The following properties are supported.

| Key | Description |
| ---- | ----------- |
| `firstName` | The customers first name. Uses the `order.CustomerInfo.FirstName` system property |
| `lastName` | The customers last name. Uses the `order.CustomerInfo.LastName` system property |
| `email` | The customers email address. Uses the `order.CustomerInfo.Email` system property |
| `company` | The company the customer works for |
| `taxCode` | The tax code of the company the customer works for |
| `telephone` | The contact telephone number of the customer |

Any missing property definition in this config block will disable that property from displaying / being editable.

For each property, the following config options are available:

| Name | Description |
| ---- | ----------- |
| `alias` | The alias of the Order property to use |
| `label` | A friendly label to display for this property in the editor interface |
| `description` | A friendly description to display for this property in the editor interface |
| `view` | Sets the name or path of a Property Editor to use when editing this property |
| `config` | Defines a JSON config for the Property Editor if required |

A fully configured Customer config block will produce a Customer summary like so:

![Order Line Property Editing](/media/screenshots/order_customer_info.png)

Clicking the Customer Details `Edit` button will display an edit interface like so:

![Order Line Property Editing](/media/screenshots/order_customer_info_editor.png)

### Billing Config Options

The Billing config block configures which Cart/Order properties relate to a Cart/Orders billing information. The following properties are supported.

| Key | Description |
| ---- | ----------- |
| `addressLine1` | Line 1 of the billing address |
| `addressLine2` | Line 2 of the billing address |
| `city` | The City of the billing address |
| `zipCode` | The Zip/Postal Code of the billing address |
| `telephone` | The telephone number of the billing address |

In addition to these properties, the `order.PaymentInfo` Country/Region will be associated with the billing address.

Any missing property definition in this config block will disable that property from displaying / being editable.

For each property, the following config options are available:

| Name | Description |
| ---- | ----------- |
| `alias` | The alias of the Order property to use |
| `label` | A friendly label to display for this property in the editor interface |
| `description` | A friendly description to display for this property in the editor interface |
| `view` | Sets the name or path of a Property Editor to use when editing this property |
| `config` | Defines a JSON config for the Property Editor if required |

A fully configured Billing config block will produce a Billing Address summary like so:

![Order Billing Address Summary](/media/screenshots/order_billing_address_summary.png)

Clicking the Customer Details `Edit` button will display an edit interface like so:

![Order Billing Address Editor](/media/screenshots/order_billing_address_editor.png)

### Shipping Config Options

The Shipping config block configures which Cart/Order properties relate to a Cart/Orders shipping information. The following properties are supported.

| Key | Description |
| ---- | ----------- |
| `enabled` | Sets whether the collection of shipping information is enabled or not. If set to `false` not shipping info will be displayed (Default: `true`) |
| `sameAsBilling` | Determines the Order property to use as a flag to indicate the shipping address is the same as the billing address |
| `firstName` | The first name of the shipping contact |
| `lastName` | The last name of the shipping contact |
| `addressLine1` | Line 1 of the shipping address |
| `addressLine2` | Line 2 of the shipping address |
| `city` | The City of the shipping address |
| `zipCode` | The Zip/Postal Code of the shipping address |
| `telephone` | The telephone number of the shipping address |

In addition to these properties, the `order.ShippingInfo` Country/Region will be associated with the shipping address.

Any missing property definition in this config block will disable that property from displaying / being editable.

For each property, except `enabled` or `sameAsBilling`, the following config options are available:

| Name | Description |
| ---- | ----------- |
| `alias` | The alias of the Order property to use |
| `label` | A friendly label to display for this property in the editor interface |
| `description` | A friendly description to display for this property in the editor interface |
| `view` | Sets the name or path of a Property Editor to use when editing this property |
| `config` | Defines a JSON config for the Property Editor if required |

For the `sameAsBilling` property, the following config options are available:

| Name | Description |
| ---- | ----------- |
| `alias` | The alias of the Order property to use |
| `label` | A friendly label to display next to a toggle switch for this property in the editor interface |
| `trueValue` | The value to expect for a `true` value |
| `falseValue` | The value to expect for a `false` value |

A fully configured Shipping config block, where the `sameAsBilling` property is `false`, will produce a Shipping Address summary like so:

![Order Shipping Address Summary](/media/screenshots/order_shipping_address_summary.png)

Where as, a fully configured Shipping config block, where the `sameAsBilling` property is `true`, will produce a Shipping Address summary like so:

![Order Shipping Address Summary - Same as Billing](/media/screenshots/order_shipping_address_summary_same_as_billing.png)

Clicking the Customer Details `Edit` button will display an edit interface like so:

![Order Shipping Address Editor](/media/screenshots/order_shipping_address_editor.png)

If the `sameAsBilling` toggle switch is toggled, the appropriate Cart/Order property will be toggled between the configured Properties true / false values, and the editor interface will be collapsed like so:

![Order Shipping Address Editor - Same as Billing](/media/screenshots/order_shipping_address_editor_same_as_billing.png)

### Notes Config Options

The Notes config block configures which Cart/Order properties relate to a Cart/Orders note information. The following properties are supported.

| Key | Description |
| ---- | ----------- |
| `customerNotes` | The property to use for customer provided notes |
| `internalNotes` | The property to use to store internal notes that shouldn't be sent to the customer |

Any missing property definition in this config block will disable that property from displaying / being editable.

For each property the following config options are available:

| Name | Description |
| ---- | ----------- |
| `alias` | The alias of the Order property to use |
| `label` | A friendly label to display for this property in the editor interface |
| `description` | A friendly description to display for this property in the editor interface |

A fully configured Notes config block will produce an editor interface like so:

![Order Notes Editor](/media/screenshots/order_notes_editor.png)

### Additional Info Config Options

The Additional Info config block configures any other Cart/Order properties you wish to display in the Cart/Order editor interface in the **Additional Info** section. For each Order Property to display you can provide the following options:

| Name | Description |
| ---- | ----------- |
| `alias` | The alias of the Order property |
| `label` | A friendly label to display for this property in the editor interface |
| `description` | A friendly description to display for this property in the editor interface |
| `isReadOnly` | Sets the property as read only and so doesn't provide a means of editing the value in the editor interface (Default: `false`) |
| `view` | Sets the name or path of a Property Editor to use when editing this property |
| `config` | Defines a JSON config for the Property Editor if required |

A fully configured Additional Info config block will produce an Additional Info summary interface like so:

![Additional Info Summary](/media/screenshots/order_additional_info_summary.png)

Clicking the Additional Info `Edit` button will display an edit interface like so:

![Additional Info Editor](/media/screenshots/order_additional_info_editor.png)

### Custom Cart/Order Editor View

If you wish to entirely replace the Cart/Order Editor view with a custom implementation you can create a Cart/Order Editor Config file with a single `view` config option which points the path of an alternative AngularJS view file to use for editing the Order.

````javascript
{
    view: '/app_plugins/myplugin/views/ordereditor/ordereditor.html'
}
````