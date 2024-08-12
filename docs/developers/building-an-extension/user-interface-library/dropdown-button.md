---
title: Dropdown Button
sidebar_position: 3
slug: /developers/building-an-extension/user-interface-library/dropdown-button
toc_min_heading_level: 2
toc_max_heading_level: 5
---

## `<DropdownButton />` Component

The `DropdownButton` is designed to create a dropdown button with customizable content and behaviors. It's built on top of the popular [`BasicDropdown` component](https://ember-basic-dropdown.com/) and supports dynamic button configurations, including the ability to use a custom button component. It manages its own internal state and handles various events related to dropdown interactions. It allows for customization of the button's appearance and behavior, including support for loading states, disabled states, and custom icons or images.

## Usage

```hbs
<DropdownButton>
    <div class="next-dd-menu">
        <div class="p-1">
            {{#each-in this.items as |item|}}
                <a href="javascript:;" class="next-dd-item" {{on "click" (dropdown-fn dd this.handleClick item)}}>
                    {{item.title}}
                </a>
            {{/each-in}}
        </div>
    </div>
</DropdownButton>
```

## Properties

<table class="docs-table">
    <thead>
        <tr>
            <th style={{ width: '22%' }}>Property</th>
            <th style={{ width: '15%' }}>Type</th>
            <th>Description</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td valign="top"><strong>type</strong></td>
            <td valign="top">String</td>
            <td valign="top">Specifies the type of button. Defaults to <code>'default'</code>.</td>
        </tr>
        <tr>
            <td valign="top"><strong>buttonSize</strong></td>
            <td valign="top">String</td>
            <td valign="top">Determines the size of the button. Defaults to <code>'md'</code>.</td>
        </tr>
        <tr>
            <td valign="top"><strong>buttonComponentArgs</strong></td>
            <td valign="top">Object</td>
            <td valign="top">Arguments passed to the custom button component, if one is used.</td>
        </tr>
        <tr>
            <td valign="top"><strong>dropdownId</strong></td>
            <td valign="top">String</td>
            <td valign="top">ID for the dropdown component.</td>
        </tr>
        <tr>
            <td valign="top"><strong>wrapperClass</strong></td>
            <td valign="top">String</td>
            <td valign="top">CSS class for the dropdown wrapper.</td>
        </tr>
        <tr>
            <td valign="top"><strong>renderInPlace</strong></td>
            <td valign="top">Boolean</td>
            <td valign="top">If <code>true</code>, the dropdown will render in place rather than as a separate node.</td>
        </tr>
        <tr>
            <td valign="top"><strong>horizontalPosition</strong></td>
            <td valign="top">String</td>
            <td valign="top">Horizontal positioning of the dropdown.</td>
        </tr>
        <tr>
            <td valign="top"><strong>verticalPosition</strong></td>
            <td valign="top">String</td>
            <td valign="top">Vertical positioning of the dropdown.</td>
        </tr>
        <tr>
            <td valign="top"><strong>calculatePosition</strong></td>
            <td valign="top">Function</td>
            <td valign="top">Function to calculate the dropdown's position.</td>
        </tr>
        <tr>
            <td valign="top"><strong>defaultClass</strong></td>
            <td valign="top">String</td>
            <td valign="top">Default CSS class for the dropdown.</td>
        </tr>
        <tr>
            <td valign="top"><strong>matchTriggerWidth</strong></td>
            <td valign="top">Boolean</td>
            <td valign="top">If <code>true</code>, the dropdown will match the width of the trigger button.</td>
        </tr>
        <tr>
            <td valign="top"><strong>onOpen</strong></td>
            <td valign="top">Function</td>
            <td valign="top">Function called when the dropdown is opened.</td>
        </tr>
        <tr>
            <td valign="top"><strong>onClose</strong></td>
            <td valign="top">Function</td>
            <td valign="top">Function called when the dropdown is closed.</td>
        </tr>
        <tr>
            <td valign="top"><strong>buttonComponent</strong></td>
            <td valign="top">Component</td>
            <td valign="top">A custom component to use for the button. <br />If not provided, a default `<Button />` component is used.</td>
        </tr>
        <tr>
            <td valign="top"><strong>text</strong></td>
            <td valign="top">String</td>
            <td valign="top">Text to display on the button.</td>
        </tr>
        <tr>
            <td valign="top"><strong>buttonClass</strong></td>
            <td valign="top">String</td>
            <td valign="top">CSS class for the button.</td>
        </tr>
        <tr>
            <td valign="top"><strong>buttonWrapperClass</strong></td>
            <td valign="top">String</td>
            <td valign="top">CSS class for the button wrapper.</td>
        </tr>
        <tr>
            <td valign="top"><strong>active</strong></td>
            <td valign="top">Boolean</td>
            <td valign="top">If <code>true</code>, the button is considered active.</td>
        </tr>
        <tr>
            <td valign="top"><strong>isLoading</strong></td>
            <td valign="top">Boolean</td>
            <td valign="top">If <code>true</code>, the button displays a loading state.</td>
        </tr>
        <tr>
            <td valign="top"><strong>disabled</strong></td>
            <td valign="top">Boolean</td>
            <td valign="top">If <code>true</code>, the button is disabled.</td>
        </tr>
        <tr>
            <td valign="top"><strong>textClass</strong></td>
            <td valign="top">String</td>
            <td valign="top">CSS class for the text within the button.</td>
        </tr>
        <tr>
            <td valign="top"><strong>helpText</strong></td>
            <td valign="top">String</td>
            <td valign="top">Text to display in a tooltip when hovering over the button.</td>
        </tr>
        <tr>
            <td valign="top"><strong>tooltipPlacement</strong></td>
            <td valign="top">String</td>
            <td valign="top">Placement of the tooltip relative to the button.</td>
        </tr>
        <tr>
            <td valign="top"><strong>img</strong></td>
            <td valign="top">String</td>
            <td valign="top">URL of an image to display on the button.</td>
        </tr>
        <tr>
            <td valign="top"><strong>imgClass</strong></td>
            <td valign="top">String</td>
            <td valign="top">CSS class for the image.</td>
        </tr>
        <tr>
            <td valign="top"><strong>alt</strong></td>
            <td valign="top">String</td>
            <td valign="top">Alt text for the image.</td>
        </tr>
        <tr>
            <td valign="top"><strong>icon</strong></td>
            <td valign="top">String</td>
            <td valign="top">Icon to display on the button.</td>
        </tr>
        <tr>
            <td valign="top"><strong>iconPrefix</strong></td>
            <td valign="top">String</td>
            <td valign="top">Prefix for the icon, useful for specifying icon sets.</td>
        </tr>
        <tr>
            <td valign="top"><strong>iconSize</strong></td>
            <td valign="top">Number</td>
            <td valign="top">Size of the icon.</td>
        </tr>
        <tr>
            <td valign="top"><strong>iconClass</strong></td>
            <td valign="top">String</td>
            <td valign="top">CSS class for the icon.</td>
        </tr>
    </tbody>
</table>

## Events

<table class="docs-table">
    <thead>
        <tr>
            <th style={{ width: '22%' }}>Event</th>
            <th>Description</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td valign="top"><strong>onRegisterAPI()</strong></td>
            <td valign="top">Calls the <code>registerAPI</code> function provided in the arguments, if it exists.</td>
        </tr>
        <tr>
            <td valign="top"><strong>onTriggerInsert()</strong></td>
            <td valign="top">Calls the <code>onTriggerInsert</code> function provided in the arguments, if it exists. Sets <code>_onTriggerInsertFired</code> to <code>true</code>. If <code>renderInPlace</code> is <code>true</code> or <code>_onInsertFired</code> is <code>false</code>, it also calls <code>onInsert</code>.</td>
        </tr>
        <tr>
            <td valign="top"><strong>onButtonInsert()</strong></td>
            <td valign="top">Calls the <code>onButtonInsert</code> function provided in the arguments, if it exists. Sets <code>_onButtonInsertFired</code> to <code>true</code>.</td>
        </tr>
        <tr>
            <td valign="top"><strong>onInsert()</strong></td>
            <td valign="top">Calls the <code>onInsert</code> function provided in the arguments, if it exists.</td>
        </tr>
    </tbody>
</table>