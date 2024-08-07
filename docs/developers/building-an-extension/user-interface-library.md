---
sidebar_position: 5
slug: /developers/building-an-extension/user-interface-library
toc_min_heading_level: 2
toc_max_heading_level: 5
---

# User Interface Library

Here you can find the most useful and commonly used UI components exposed by `@fleetbase/ember-ui` library. These components can be used to build composable and clean user interfaces for Fleetbase Extensions.

### `<Button />` Component

The `Button` component renders a HTML `<button />` element. It supports various states and styles, including loading indicators, icons, and secondary styling. This component also integrates with tooltips to provide additional help text. The `Button` manages its own internal state to determine if it should be disabled or if certain elements like icons should be displayed. It also supports various button types and sizes, and can handle click events and setup actions.

#### Usage

```hbs
<Button @type="primary" @text="Click Me" @onClick={{this.doSomething}} @helpText="This button does something..." />
```

#### Events

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

#### Properties

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

#### Types

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

### `<DropdownButton />` Component

The `DropdownButton` is designed to create a dropdown button with customizable content and behaviors. It's built on top of the popular [`BasicDropdown` component](https://ember-basic-dropdown.com/) and supports dynamic button configurations, including the ability to use a custom button component. It manages its own internal state and handles various events related to dropdown interactions. It allows for customization of the button's appearance and behavior, including support for loading states, disabled states, and custom icons or images.

#### Usage

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

#### Properties

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

#### Events

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

### `<UploadButton />` Component

The UploadButton component renders a button for uploading files. It provides visual feedback during the upload process and supports different styling options. The component can be customized with icons, button text, and additional styling. It also handles file uploads by displaying an upload indicator or the button's icon based on the upload status.

#### Usage 

```hbs
<UploadButton @accept="jpg,png,svg,gif" @onFileAdded={{this.onFileAdded}} />
```

Handling the upload:

```js
@action onFileAdded(file) {
    this.fetch.uploadFile.perform(
        file,
        {
            path: 'uploads',
            type: 'image',
        },
        (uploadedFile) => {
            // Do something with uploaded file
        },
        () => {
            // Handle upload failure
            file.queue.remove(file);
        }
    );
}
```

#### Properties

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
            <td valign="top"><strong>name</strong></td>
            <td valign="top">String</td>
            <td valign="top">The name attribute for the file input.</td>
        </tr>
        <tr>
            <td valign="top"><strong>accept</strong></td>
            <td valign="top">String</td>
            <td valign="top">Specifies the types of files that the file input should accept.</td>
        </tr>
        <tr>
            <td valign="top"><strong>onFileAdded</strong></td>
            <td valign="top">Function</td>
            <td valign="top">Function called when a file is added to the upload queue.</td>
        </tr>
        <tr>
            <td valign="top"><strong>labelClass</strong></td>
            <td valign="top">String</td>
            <td valign="top">CSS class to apply to the label element within the button.</td>
        </tr>
        <tr>
            <td valign="top"><strong>type</strong></td>
            <td valign="top">String</td>
            <td valign="top">Specifies the button type. Defaults to <code>'default'</code>.</td>
        </tr>
        <tr>
            <td valign="top"><strong>size</strong></td>
            <td valign="top">String</td>
            <td valign="top">Size of the button. Defaults to <code>'sm'</code>.</td>
        </tr>
        <tr>
            <td valign="top"><strong>icon</strong></td>
            <td valign="top">String</td>
            <td valign="top">Icon to display on the button. Defaults to <code>"image"</code>.</td>
        </tr>
        <tr>
            <td valign="top"><strong>iconClass</strong></td>
            <td valign="top">String</td>
            <td valign="top">CSS class for the icon.</td>
        </tr>
        <tr>
            <td valign="top"><strong>buttonText</strong></td>
            <td valign="top">String</td>
            <td valign="top">Text to display on the button when no file is being uploaded.</td>
        </tr>
        <tr>
            <td valign="top"><strong>hideButtonText</strong></td>
            <td valign="top">Boolean</td>
            <td valign="top">If <code>true</code>, hides the button text.</td>
        </tr>
        <tr>
            <td valign="top"><strong>outline</strong></td>
            <td valign="top">Boolean</td>
            <td valign="top">If <code>true</code>, applies an outline style to the button.</td>
        </tr>
    </tbody>
</table>

### `<FileDropzone />` Component
### `<InputGroup />` Component
### `<MoneyInput />` Component
### `<PhoneInput />` Component
### `<Select />` Component
### `<ModelSelect />` Component
### `<Toggle />` Component
### `<Checkbox />` Component
### `<Badge />` Component
### `<Spinner />` Component
### `<ProgressBar />` Component
### `<ContentPanel />` Component
### `<Overlay />` Component
### `<DatePicker />` Component
### `<DateTimeInput />` Component
### `<Image />` Component
### `<TipTapEditor />` Component
### `<Table />` Component
### `<Pagination />` Component
### `<Modal />` Component
### `<Layout />` Components
### `<Attach />` Components