---
layout: post
title: Getting Started with Vue 3 Workflow Designer | Syncfusion®
description: Set up a Vue 3 Workflow Designer project with Vite, install the package and theme, and then load and run your first workflow.
control: Workflow Designer
platform: diagram-sdk
documentation: ug
domainurl: https://help.syncfusion.com/diagram-sdk
---

# Getting Started with Vue 3 Workflow Designer

This section explains how to create a Workflow Designer in a Vue 3 application, load a workflow into it, and run the workflow.

## Prerequisites

Make sure the following tools are installed on your machine:

* [Node.js](https://nodejs.org/en) (the latest long-term support version is recommended)
* [Visual Studio Code](https://code.visualstudio.com)

## Dependencies

The Workflow Designer package depends on the following packages:

```text
|-- @syncfusion/ej2-vue-workflow-designer
    |-- @syncfusion/ej2-vue-base
    |-- @syncfusion/ej2-vue-diagrams
    |-- @syncfusion/ej2-vue-buttons
    |-- @syncfusion/ej2-vue-inputs
    |-- @syncfusion/ej2-vue-dropdowns
    |-- @syncfusion/ej2-vue-calendars
    |-- @syncfusion/ej2-vue-navigations
    |-- @syncfusion/ej2-vue-popups
    |-- @syncfusion/ej2-vue-interactive-chat
```

## Set up the development environment

Use [Vite](https://vitejs.dev/) to create and manage Vue 3 applications.

Create a new Vue 3 application using the following command:

{% tabs %}
{% highlight bash tabtitle="CMD" %}

npm create vite@latest my-workflow-app -- --template vue

{% endhighlight %}
{% endtabs %}

If Vite prompts you to install dependencies and start the project immediately, select **No**. Dependencies are installed in the next step instead, so that the Syncfusion package can be added together with the rest of the application dependencies.

Navigate to the project folder and install the application dependencies:

{% tabs %}
{% highlight bash tabtitle="CMD" %}

cd my-workflow-app
npm install

{% endhighlight %}
{% endtabs %}

N> If you prefer TypeScript instead of JavaScript, create the application using `npm create vite@latest my-workflow-app -- --template vue-ts`. For TypeScript-specific guidance, refer to the [Vite Vue-TS template](https://vitejs.dev/guide/) documentation.

## Add the Syncfusion<sup style="font-size:70%">&reg;</sup> Workflow Designer package

Install the Workflow Designer package from npm:

{% tabs %}
{% highlight bash tabtitle="CMD" %}

npm install @syncfusion/ej2-vue-workflow-designer --save

{% endhighlight %}
{% endtabs %}

## Import the Syncfusion<sup style="font-size:70%">&reg;</sup> CSS styles

The Workflow Designer needs Syncfusion<sup style="font-size:70%">&reg;</sup> theme styles to display correctly. Install the Tailwind 3 theme package:

{% tabs %}
{% highlight bash tabtitle="CMD" %}

npm install @syncfusion/ej2-tailwind3-theme

{% endhighlight %}
{% endtabs %}

Then add the following import to the **src/App.vue** file:

{% tabs %}
{% highlight html tabtitle="App.vue" %}

<style>
  @import "../node_modules/@syncfusion/ej2-tailwind3-theme/styles/workflow-designer/index.css";
</style>

{% endhighlight %}
{% endtabs %}

N> The theme package includes the styles for the Workflow Designer and the controls it depends on, so you don't need a separate import for each dependency. To use a different theme, replace the theme package in the import path with the package of the theme you want. For example, to use the Material 3 theme, import **@syncfusion/ej2-material3-theme/styles/workflow-designer/index.css**. For the list of available themes, see [Themes](https://ej2.syncfusion.com/vue/documentation/appearance/theme).

## Add the HTML element

In a Vite Vue application, the root element is defined in **index.html** as `<div id="app"></div>`, and the application is mounted from `src/main.js`. The Workflow Designer component is rendered inside this root element by the Vue application.

## Create the Workflow Designer

Import `WorkflowDesignerComponent` from `@syncfusion/ej2-vue-workflow-designer` and use it in your component. Then add the `ejs-workflowdesigner` element to the template.

Replace the entire contents of **src/App.vue** with the following code:

{% tabs %}
{% highlight html tabtitle="App.vue" %}

<template>
  <ejs-workflowdesigner
    id="workflowDesigner"
    width="100%"
    height="640px"
  />
</template>

<script setup>
import { WorkflowDesignerComponent as EjsWorkflowdesigner } from '@syncfusion/ej2-vue-workflow-designer';
</script>

<style>
  @import "../node_modules/@syncfusion/ej2-tailwind3-theme/styles/workflow-designer/index.css";
</style>

{% endhighlight %}
{% endtabs %}

In this example:

* [`width`](https://ej2.syncfusion.com/vue/documentation/api/workflow-designer/#width) and [`height`](https://ej2.syncfusion.com/vue/documentation/api/workflow-designer/#height) set the size of the designer. Both accept pixels or any CSS length.

N> The `WorkflowDesignerComponent as EjsWorkflowdesigner` alias is required for `<script setup>`, because the `<ejs-workflowdesigner>` element name in the template must match the registered component alias. The Workflow Designer component must also have a valid height; if the height is not set, the designer may not be visible.

## Load a workflow

A workflow is a JSON object that contains steps, connections, and optional sticky notes. To load one, replace the contents of **src/App.vue** with the following code:

{% tabs %}
{% highlight html tabtitle="App.vue" %}

<template>
  <ejs-workflowdesigner
    id="workflowDesigner"
    ref="workflowDesigner"
    height="640px"
  />
</template>

<script setup>
import { WorkflowDesignerComponent as EjsWorkflowdesigner } from '@syncfusion/ej2-vue-workflow-designer';
import { onMounted, ref } from 'vue';

const workflowDesigner = ref(null);

const workflow = {
    schemaVersion: '1.1',
    id: 'order-check',
    name: 'Order check',
    workflowSteps: [
        { id: 'start', stepType: 'ManualTrigger', name: 'Start', position: { x: 100, y: 200 } },
        {
            id: 'getOrder', stepType: 'ApiRequest', name: 'Get order', position: { x: 340, y: 200 },
            props: { method: 'GET', url: 'https://example.com/api/orders/1' }
        },
        {
            id: 'isPaid', stepType: 'Condition', name: 'Is paid?', position: { x: 580, y: 200 },
            props: {
                conditions: [{ field: '{{$.previousStep.response.body.paid}}', valueType: 'Boolean', operator: 'IsTrue' }]
            }
        },
        { id: 'done', stepType: 'End', name: 'Done', position: { x: 820, y: 120 } },
        {
            id: 'remind', stepType: 'Notify', name: 'Send reminder', position: { x: 820, y: 280 },
            props: { channel: 'Email', to: 'customer@example.com', message: 'Your order is awaiting payment.' }
        }
    ],
    workflowConnections: [
        { id: 'c1', sourceStepId: 'start', targetStepId: 'getOrder' },
        { id: 'c2', sourceStepId: 'getOrder', targetStepId: 'isPaid' },
        { id: 'c3', sourceStepId: 'isPaid', sourcePort: 'true', targetStepId: 'done' },
        { id: 'c4', sourceStepId: 'isPaid', sourcePort: 'false', targetStepId: 'remind' }
    ]
};

onMounted(() => {
    workflowDesigner.value.ej2Instances.importWorkflow(workflow);
});

{% endhighlight %}
{% endtabs %}

In this example:

* `workflowSteps` defines five steps. Each step has an `id`, a `stepType`, a display `name`, and a `position` on the canvas. For every step type and its settings, see [Workflow steps](./workflow-steps).
* `props` holds the settings of a step, such as the request `method` and `url` of the `ApiRequest` step.
* The `Condition` step reads the API response with the expression `{{$.previousStep.response.body.paid}}`. For more about expressions, see [Expressions and runtime context](./expressions).
* `workflowConnections` links the steps. On the `Condition` step, `sourcePort` selects the `true` or `false` branch. For more about ports, see [Connections and ports](./connections).
* [`importWorkflow`](https://ej2.syncfusion.com/vue/documentation/api/workflow-designer/#importworkflow) loads the workflow into the designer. The public instance of the component is accessed through the `ej2Instances` property of the component reference.

## Run the application

Run the application with the following command:

{% tabs %}
{% highlight bash tabtitle="CMD" %}

npm run dev

{% endhighlight %}
{% endtabs %}

Open the local URL shown in the terminal, such as `http://localhost:5173`. The designer displays the order-check workflow.

To run the workflow, click **Execute** in the top toolbar or press <kbd>Ctrl</kbd> + <kbd>Enter</kbd>. The execution log shows the result of each step.

[VERIFY: Add a screenshot of the order-check workflow after a run. Suggested alt text: "Order-check workflow on the canvas, with the execution log listing the result of each step."]

N> The sample request uses a placeholder URL, so the **Get order** step fails and the execution log shows the error. To see the full run, replace the URL with an API that returns a JSON object with a `paid` field and allows cross-origin requests from your page.

If `npm run dev` fails, run `npm install` again to make sure all dependencies are installed. If the default port is already in use, stop the other process or change the port in the Vite configuration.

## See also

* [Workflow steps](./workflow-steps)
* [Connections and ports](./connections)
* [Running workflows](./execution)
* [Default Functionalities demo](https://ej2.syncfusion.com/vue/demos/#/tailwind3/workflow-designer/default-functionalities.html)
