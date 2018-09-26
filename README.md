# Sections

In the new locomotivecms version, **Sections** are a key concept.
Like snippets, sections includes a piece of code in a page, and in addition, provide a powerful way to let users update their website on the fly by changing texts and images, but also by adding new sections, re-ordering them and updating their content.

## What is a section

A section is a piece of **HTML content** that comes with a **JSON definition**. It is integrated in a page of a locomotivecms site.

In a section definition you explicitly detail settings of your section, explaining how to tweak the HTML content.

Settings can be texts, images, blocks of content, *etc*. They will be customized via the locomotivecms back office **editor**.

Sections enpower your users into website manager. Not only by giving them the abilitity to change the text of the website but also the freedom to change the appearence of pages: the number of blocks, their order, their images, their text....

By defining sections you help your user to test, manage and deploy new pages faster.

As time goes, you will have a growing number of sections to provide scalable websites and build faster from a section catalogue.

**here be screenshots**

To include sections in a page, you have 3 options:

- Let a complete freedom of choice to your user by defining a section_dropzone, where users will choose what section to add from a catalogue you provide.

- Fix section that will be editable by users but will not be movable.

- Define global sections that will be available accross all pages.

## Add a section

### The wagon generator

The fastest way to start a new section is by using the section generator provided by wagon.

The following command creates a new section named `hello_world`

`$bundle exec wagon generate section hello_world`

You will be asked various questions and the generator will build a section with some code example that suits your needs.
You should now see a new file named `hello_world.liquid` in the `/sections` folder.

## Define a sections

A section is composed of two essential elements: a JSON definition and an HTML content, all packaged inside a liquid file like so :

```
---
{
  JSON Definition
}
---
HTML Content
```

The name of the file will be used as id in the Locomotivecms engine.

#### - Json definition

The Json definition of a section contains all informations about how your user will customize your section. You can control what shows up on the editor view and what are the settings you provide to customize the page.


##### Example of a section definition

```
{
  "name": "Hello world",
  "class": "hello-worl-css-top-class",
  "icon": "image_text",
  "settings": [
    {
      "label": "Greeting Word",
      "id": "greeting_word",
      "type": "text",
      "html": true,
      "nb_rows": 5,
      "default": "<strong>Hello</strong>"
    },
    {
      "label": "Avatar",
      "id": "avatar",
      "type": "image_picker",
      "height": "100",
      "width": "100",
      "default": "/samples/avatars/me"
    }
  ],
  "blocks": [
    {
      "type": "person",
      "name": "Someone",
      "settings": [
        {
          "label": "A name",
          "id": "name",
          "type": "text",
          "default": "World"
        }
      ]
    }
  ],
  "presets": [
    {
      "name": "Pirate Greetings",
      "category": "colorfull",
      "settings": {
        "greeting_word": "Ahoy",
        "avatar": "/samples/avatar/pirate"
      },
      "blocks": [
        {
          "type": "person",
          "settings": {
            "name": "mate"
          }
        }
      ]
    }
  ],
  "default": {
    "settings": {
      "greeting_word": "Hi",
      "avatar": "/samples/avatar/anonyme"
    },
    "blocks": [
      {
        "type": "person",
        "settings": {
          "name": "there"
        }
      }
    ]
  }
}
```

##### Mendatories:

- A **name** that acts as label in the editor.
- An array of **settings** that will be editable for customization. **Settings** should have an *id*, a *label*, and a *type*. Type should be one of the following:

      Type      |       Use
--------------- | ---------------------------------------------------------------------------------------------------------
`text`          | Display a text input. <br> Texts can be edited with HTML by passing `"html": true` in the setting definition, this will enable severals format options in the editor. <br> You can choose `<br>` over `<p>` by using the `"line_break": true` option. <br> You can also choose the size of the text area as a number of rows: `"nb_rows": 10`
`radio`         | Display a radio button in the editor with the options provided with : ```"options": [{"label": "option 1","value": "1"},{"label": "option 2", "value": "2"}]```
`checkbox`      | Display a checkbox in the editor. Values of the checkbox are true/false.
`image_picker`  | Add an image picker to the editor. <br> You can add a cropper to let your users resize their images. To do so, add `"height": "100", "width": "100"` to your settings to let user crop image to any size with same aspect ratio (the image must be larger than 100x100).
`url`           | Add an url picker to the editor.

- An array of **blocks**. **Blocks** must have a *name*, a *type* and an array of *settings*. If you have define multiple block types in your section, the editor will show a button to add blocks from a list of block types. Once chosen, your user can edit them using the settings you provided. When editing the section, the user can add/order/remove the **blocks**.
*Here be screenshots*

##### Content definition

To have your section listed in the editor when a user click on "*add a section*" on a dropzone, you have to provide at least one **presets**. **presets** come with a name to label them in the sections list, and a category to sort them. Inside a **presets** you can override settings and define what blocks you want your user to start with.

If your section is fixed or global, it is recommended to add a **default** to the section definition. **Default** is a hash that can override settings' default and define some blocks to start with, like **presets**.

##### Optionals top level properties:

- ***class*** : The top css classes that will be added to the parent HTML Element that wraps the section.
- ***icon*** : The icon used to represent the section in the editor.
- ***keep_icon*** : Boolean to force the display of the icon in the editor instead of the first image setting.
- ***keep_name*** : Boolean to force the display of the section name instead of the first text setting.
- ***blocks_label*** : Label to display at the top of the block selection in the editor.

#### - HTML content

Sections use the same liquid syntax as the rest of the locomotivecms framework.
When in a section HTML you are provided with a `section` object that contains all the content of your section.
Example:

```
<h2>{{ section.settings.greeting_word }}</h2>
{% for block in section.blocks %}
  {{ block.settings.name }}
{% endfor %}
```
##### section settings

You can call them with `{{ section.settings.my_setting }}`
For exemple if a section was define like previously, you can call the text value with : `{{ section.settings.greetings_word }}`

##### section blocks

The blocks of your section are stored in an array and can be iterated through with `{% for block in section.blocks %}`
The block object provided contains all the settings inside a settings hash. With the previous example, you can access the settings with `{{ block.settings.name }}`.

##### section meta?

There is some extra data you can access in your section or block like type and name :`{{ section.type }}` or `{{ block.type }}`

## Call a section

There is three tags that can be used to call your section:

### Fix sections

The tag `{% section 'hello_world %}` is used to add a section to a page. This section will be fixed in the page and your user will only be able to edit the content without being able to order or remove the section.
You can call multiple times the same section on the same page. because of that, it is recommended to pass an id to the tag to keep tracks of the section content as it may be moved aroud the page in futur developpements and may be mixed up with section of the same type : `{% section 'hello_world', id: 'morning' %}`

### Section dropzone

Here lie the true section's power !
attention preset


To let your user manage all sections that will be included in a page, add a tag `{% sections_dropzone %}`. This will add a section selector in the editor and your users will be able to add any sections provided in the sections folder of your project.
Then it will be easy for them to edit the content, reorder the sections, add or remove them.

### Global sections

If you want to add a section that holds its content all over the website, you can use the tag `{% global_section 'hello_word' %}`
This will display a fixed section in the editor but the content your user edits will be shared across pages. This is useful for header, footer and nav sections.

### Locomotive wrapping

The tag you provide will decide how locomotivecms will include your section in the page HTML.
If you provide a section dropzone tag, locomotivecms will wrap your sections in a *div* with a class *locomotive-sections*. Then each sections inside your section dropzone will be appended within a div with a class *locomotive-section* and their type as a data-attribute *data-locomotive-section-type*. You can provide additional classes to the top div by using the class property in the section's JSON definition.
If you provide section with the fixed or global section tag, the HTML content will be wrapped inside a div with the section's id optionally passed as the div's id. Again you can add classes to this div by using the class property in the JSON definition.


## Content of a section:

If you define a global section, the content will be loaded from the default definition of your section if no content has been provided by the user.
If you place a dropzone in your page, you can fill it in the yaml definition of your page. Use the sections_dropzone_content field to define the sections you want to show by default if no content was provided.
If you place fixed sections in your page, you can also fill them in the yaml definition. Use the sections_content field to define what should be the default content. You can identify the fixed section of your page by passing their id in the yaml, or their type if no id was given.

Example:

```
sections_content: >
  {
    "fancy": {
      "settings": {
        "greeting_word": "Salutation",
        "avatar": "/samples/avatar/gentleman",
      },
      "blocks": [
        "type": "person",
        "settings" {
          "name": "sir"
        }
      ]
    }
  }
```

## Deploy a Section

`bundle exec wagon deploy <ENV> -r <PAGE> -f hello_world.liquid -d -v`

you can filter the section you want to push with the -f option

## JS integration

The editor is a dynamic ReactJs Component that will modify your website in an iframe as your user add/remove/edit the section you provide.
To follow all this changes in the iframe, the editor will fire multiple events types you can listen to.
To register your section's javascript to this event you can add a new javascript file in assets/javascript/sections, then export it in sections/index.js, and finally add `theme.registerSection('hello_world', Section.HelloWorld);` to app.js

Events you can register are : load, unload, select, deselect, reorder, blockSelect, blockDeselect.

This way you can adapt your javascript to this changes.
