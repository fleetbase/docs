---
title: Upload Button
sidebar_position: 4
slug: /developers/building-an-extension/user-interface-library/upload-button
toc_min_heading_level: 2
toc_max_heading_level: 5
---

## `<UploadButton />` Component

The UploadButton component renders a button for uploading files. It provides visual feedback during the upload process and supports different styling options. The component can be customized with icons, button text, and additional styling. It also handles file uploads by displaying an upload indicator or the button's icon based on the upload status.

## Usage 

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