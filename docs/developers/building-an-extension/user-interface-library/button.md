---
title: Button
sidebar_position: 3
slug: /developers/building-an-extension/user-interface-library/button
toc_min_heading_level: 2
toc_max_heading_level: 5
---

## `<Button />` Component

The `Button` component renders a HTML `<button />` element. It supports various states and styles, including loading indicators, icons, and secondary styling. This component also integrates with tooltips to provide additional help text. The `Button` manages its own internal state to determine if it should be disabled or if certain elements like icons should be displayed. It also supports various button types and sizes, and can handle click events and setup actions.

## Usage

```hbs
<Button @type="primary" @text="Click Me" @onClick={{this.doSomething}} @helpText="This button does something..." />
```

## Events

The button has two built in events, but also supports all HTML `<button />` events through the use of the `{{on}}` modifier.

<table class="docs-table">
    <thead>
        <tr>
            <th style={{ width: '15%' }}>Event</th>
            <th>Description</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td valign="top"><strong>onInsert</strong></td>
            <td valign="top">
                Fired when the Button is rendered and inserted into view.

                <strong>Example Usage</strong>
                ```hbs
                <Button @text="Click Me" @onInsert={{this.doSomething}} />
                ```
            </td>
        </tr>
        <tr>
            <td valign="top"><strong>onClick</strong></td>
            <td valign="top">
                Fired when a user clicks the button.

                <strong>Example Usage</strong>
                ```hbs
                <Button @text="Click Me" @onClick={{this.doSomething}} />
                ```
                ```hbs
                <Button @text="Click Me" {{on "hover" this.doSomething}} />
                ```
            </td>
        </tr>
    </tbody>
</table>

## Properties

<table class="docs-table">
    <thead>
        <tr>
            <th style={{ width: '15%' }}>Property</th>
            <th style={{ width: '10%' }}>Type</th>
            <th style={{ width: '75%' }}>Description</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td valign="top"><strong>isLoading</strong></td>
            <td valign="top">Boolean</td>
            <td valign="top">Indicates whether the button is in a loading state. If <code>true</code>, shows a spinner and disables interactions.</td>
        </tr>
        <tr>
            <td valign="top"><strong>disabled</strong></td>
            <td valign="top">Boolean</td>
            <td valign="top">Determines if the button should be disabled. If <code>true</code>, the button is not clickable.</td>
        </tr>
        <tr>
            <td valign="top"><strong>type</strong></td>
            <td valign="top">String</td>
            <td valign="top">Specifies the button type. Defaults to <code>'default'</code>. If set to <code>'secondary'</code>, the button has secondary styling.</td>
        </tr>
        <tr>
            <td valign="top"><strong>icon</strong></td>
            <td valign="top">String</td>
            <td valign="top">The icon to display within the button, shown if <code>isLoading</code> is <code>false</code>.</td>
        </tr>
        <tr>
            <td valign="top"><strong>iconPrefix</strong></td>
            <td valign="top">String</td>
            <td valign="top">The prefix for the icon, useful for specifying icon sets.</td>
        </tr>
        <tr>
            <td valign="top"><strong>iconSize</strong></td>
            <td valign="top">Number</td>
            <td valign="top">The size of the icon.</td>
        </tr>
        <tr>
            <td valign="top"><strong>iconRotation</strong></td>
            <td valign="top">String</td>
            <td valign="top">Rotation angle for the icon.</td>
        </tr>
        <tr>
            <td valign="top"><strong>iconFlip</strong></td>
            <td valign="top">String</td>
            <td valign="top">Flip orientation for the icon.</td>
        </tr>
        <tr>
            <td valign="top"><strong>iconSpin</strong></td>
            <td valign="top">Boolean</td>
            <td valign="top">Whether the icon should spin.</td>
        </tr>
        <tr>
            <td valign="top"><strong>text</strong></td>
            <td valign="top">String</td>
            <td valign="top">The text to display inside the button.</td>
        </tr>
        <tr>
            <td valign="top"><strong>textClass</strong></td>
            <td valign="top">String</td>
            <td valign="top">CSS class to apply to the text element within the button.</td>
        </tr>
        <tr>
            <td valign="top"><strong>helpText</strong></td>
            <td valign="top">String</td>
            <td valign="top">Text to display in a tooltip when hovering over the button.</td>
        </tr>
        <tr>
            <td valign="top"><strong>exampleText</strong></td>
            <td valign="top">String</td>
            <td valign="top">Example text to display alongside <code>helpText</code> in the tooltip.</td>
        </tr>
        <tr>
            <td valign="top"><strong>tooltipPlacement</strong></td>
            <td valign="top">String</td>
            <td valign="top">Placement of the tooltip relative to the button.</td>
        </tr>
        <tr>
            <td valign="top"><strong>wrapperClass</strong></td>
            <td valign="top">String</td>
            <td valign="top">Additional CSS class for the button wrapper.</td>
        </tr>
        <tr>
            <td valign="top"><strong>outline</strong></td>
            <td valign="top">Boolean</td>
            <td valign="top">If <code>true</code>, the button uses an outline style.</td>
        </tr>
        <tr>
            <td valign="top"><strong>size</strong></td>
            <td valign="top">String</td>
            <td valign="top">Size of the button. Defaults to <code>'sm'</code> if not specified.</td>
        </tr>
        <tr>
            <td valign="top"><strong>buttonType</strong></td>
            <td valign="top">String</td>
            <td valign="top">The type attribute for the button element, defaults to <code>"button"</code>.</td>
        </tr>
        <tr>
            <td valign="top"><strong>responsive</strong></td>
            <td valign="top">Boolean</td>
            <td valign="top">If <code>true</code>, hides the text on smaller screens.</td>
        </tr>
    </tbody>
</table>

## Types

<table class="docs-table">
    <thead>
        <tr>
            <th style={{ width: '15%' }}>Type</th>
            <th>Description</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td valign="top"><strong>secondary</strong></td>
            <td valign="top">Styling for a secondary interaction.</td>
        </tr>
        <tr>
            <td valign="top"><strong>primary</strong></td>
            <td valign="top">Styling for a primary interaction.</td>
        </tr>
        <tr>
            <td valign="top"><strong>success</strong></td>
            <td valign="top">Styling for a successful interaction.</td>
        </tr>
        <tr>
            <td valign="top"><strong>warning</strong></td>
            <td valign="top">Styling for a warning interaction.</td>
        </tr>
        <tr>
            <td valign="top"><strong>danger</strong></td>
            <td valign="top">Styling for a dangerous interaction.</td>
        </tr>
        <tr>
            <td valign="top"><strong>magic</strong></td>
            <td valign="top">Styling for a "magical" interaction.</td>
        </tr>
        <tr>
            <td valign="top"><strong>black</strong></td>
            <td valign="top">Styling for a dark styled button.</td>
        </tr>
        <tr>
            <td valign="top"><strong>transparent</strong></td>
            <td valign="top">Styling for a transparent styled button.</td>
        </tr>
        <tr>
            <td valign="top"><strong>link</strong></td>
            <td valign="top">Styling to render the button as a link (no margin, padding, borders, shadow, or background).</td>
        </tr>
    </tbody>
</table>