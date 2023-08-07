# NewBlockCms

# ADD New CMS Block

## File required :

- `config/packages/integral_service_cms_core.yaml`
- `src/Form/BlockContent/YourFileType.php`
- `src/templates/Blocks/yourTemplateName/_form.html.twig`
- `src/templates/Blocks/yourTemplateName/default.html.twig`

## CMS Config :

`config/packages/integral_service_cms_core.yaml`

This code defines parameters and configuration for a content management system (CMS).
It declares content block types, specifies how they should be rendered
and displayed in the CMS user interface.
Each block type has a label, a default rendering model.
**Example:**

``` yaml
#config/packages/integral_service_cms_core.yaml
parameters:
  only_root_blocks: &only_root_blocks
    - "container"
    - "textSection"
    - "horizontalBand"
    - "keyNumbersSection"
    - "formContainerSection"
    - "pageType"
    - "standalone"

integral_service_cms_core:

  block_types:
    root:
      only: *only_root_blocks

    yourBlock:
      label: 'cms.blocks.yourTemplateName.label'
      default_template: 'default'
      available_templates:
        default:
          label: 'cms.blocks.yourTemplateName.template.default'
          template: 'Blocks/yourTemplateName/default.html.twig'
      container: false
      form: 'App\Form\BlockContent\YourFileType'
      formTemplate: 'Blocks/yourTemplateName/_form.html.twig'
      
  menu:
    config:
      extra_serializable_prop: []

    themes:
      menuNav:
        label: 'Menu.Nav'
        template: 'cms/IntegralServiceCmsEaBundle/Menu/theme/menunavbar.html.twig'
```

### Parameters to know :

- `only_root_blocks: &only_root_blocks`,  all blocks set to this parameter give the role of Root, which allows the block to be declared as the first block.
``` yaml
#config/packages/integral_service_cms_core.yaml
parameters:
  only_root_blocks: &only_root_blocks
    - "container"
    - "textSection"
    - "horizontalBand"
    - "keyNumbersSection"
    - "formContainerSection"
    - "pageType"
    - "standalone"
```
_________________

- `container: false/true`, all blocks set to this parameter give the block the role of parent, it can contain other child blocks.
```yaml
#config/packages/integral_service_cms_core.yaml
 yourBlock:
      #Your Other parametres
      container: false
    #Your Other parametres
    
```

_________________

- ` menu: config: extra_serializable_prop: []`, Used to configure other blocks apart from the `page` section, here we manage the menus.

``` yaml
#config/packages/integral_service_cms_core.yaml
menu:
config:
extra_serializable_prop: []

    themes:
      menuNav:
        label: 'Menu.Nav'
        template: 'cms/IntegralServiceCmsEaBundle/Menu/theme/menunavbar.html.twig'
```

_________________

## Block Content:

`src/Form/BlockContent/YourFileType.php`

Allows to configure the different parameters that our block will take. Allows us to declare the options in the `_form.twig.html`. 
**Example:**

```php
<?php

namespace App\Form\BlockContent;

use IntegralService\CmsCoreBundle\Form\BlockContent\AbstractBlockContentType;
use IntegralService\CmsEaBundle\Form\BlockContent\FormTrait;
use Symfony\Component\Form\Extension\Core\Type\ChoiceType;


class BoxedTitleType extends AbstractBlockContentType
{
    use FormTrait;

    protected function getFields(): array
    {
        return [
            'alignment' => [
                'type' => ChoiceType::class,
                'options' => [
                    'label' => 'cms.blocks.attr.alignment',
                    'choices' => [
                        'cms.blocks.attr.alignment.start' => 'start',
                        'cms.blocks.attr.alignment.center' => 'center',
                        'cms.blocks.attr.alignment.end' => 'end',
                    ],
                ],
            ],
            'marginTop' => [
                'type' => ChoiceType::class,
                'options' => [
                    'label' => 'cms.blocks.attr.margin_top',
                    'choices' => $this->margins,
                ],
            ],
            'marginBottom' => [
                'type' => ChoiceType::class,
                'options' => [
                    'label' => 'cms.blocks.attr.margin_bottom',
                    'choices' => $this->margins,
                ],
            ],
        ];
    }
}

```

### Parameters to know :

Here we will focus on the `choices =>`.

```php
'choices' => [
                        'cms.blocks.attr.alignment.start' => 'start',
                        'cms.blocks.attr.alignment.center' => 'center',
                        'cms.blocks.attr.alignment.end' => 'end',
                    ],
```

- `cms.blocks.attr.alignment.start`, declaration of all options.
- `=> 'value used in default.html.twig'`

_________________

## _form.html.twig

`src/templates/Blocks/yourTemplateName/_form.html.twig`

The editing form will allow CMS users to customise the appearance and layout of
the block when they create or edit a page with this type of content block.

```html
<!-- src/templates/Blocks/yourTemplateName/_form.html.twig -->

{% extends "@IntegralServiceCmsCore/BlockType/_form.html.twig" %}

{% block formContent %}
    {% if form.content is defined %}

        <div class="row">
            <div class="col-md-4">
                {{ form_row(form.content.data.alignment) }}
            </div>
            <div class="col-md-4">
                {{ form_row(form.content.data.marginTop) }}
            </div>
            <div class="col-md-4">
                {{ form_row(form.content.data.marginBottom) }}
            </div>
        </div>
        {{ form_widget(form.content.twigTemplate) }}
    {% endif %}
{% endblock %}
```

## default.html.twig

`src/templates/Blocks/yourTemplateName/default.html.twig`

The purpose of this code is to apply the custom CSS styles select in
_form.html.twig to the block.

```html
<!-- src/templates/Blocks/yourTemplateName/default.html.twig -->

{% set text_align = 'text-' ~ content.content.textAlign %}

{% set margin_top = ' mt-0' %}
{% set margin_bottom = ' mb-0' %}

{% if content.content.marginTop != 'none' %}
    {% set margin_top = ' mt-' ~ content.content.marginTop %}
{% endif %}

{% if content.content.marginBottom != 'none' %}
    {% set margin_bottom = ' mb-' ~ content.content.marginBottom %}
{% endif %}

<h1 class="{{ text_align }}{{ margin_top }}{{ margin_bottom }}">
    {{ content.content.title }}
</h1>
```
