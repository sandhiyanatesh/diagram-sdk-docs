---
layout: post
title: Workflow Steps in Vue 3 Workflow Designer | Syncfusion®
description: Learn about the trigger, action, flow, human, and AI steps in the Syncfusion Vue Workflow Designer, including their settings and output ports.
control: Workflow Designer
platform: diagram-sdk
documentation: ug
domainurl: https://help.syncfusion.com/diagram-sdk
---

# Workflow Steps in Vue 3 Workflow Designer

A step is a unit of work in a workflow. Every step is a `WorkflowStep` object with the following properties:

| Property | Type | Description |
|---|---|---|
| `id` | `string` | A unique identifier. See [Identifier rules](#identifier-rules). |
| `stepType` | `WorkflowStepType` | What the step does. Defaults to `'ManualTrigger'`. |
| `name` | `string` | The display name. |
| `props` | `object` | The step settings. They depend on `stepType` and must be JSON-safe. |
| `customProps` | `object` | Application data that's saved with the step but not used by the designer. |
| `position` | `{ x, y }` | The position on the canvas. |
| `size` | `{ width, height }` | The step size. When it isn't set, the default size is used. |
| `errorHandling` | `WorkflowStepErrorHandling` | Timeout, retry, and continue-on-error behavior. See [Error handling](./execution#error-handling). |

String settings accept [expressions](./expressions), such as `{{$.previousStep.response.body.id}}`.

## Identifier rules

The `id` of a step, connection, or sticky note must follow these rules:

* It must be unique across all steps, connections, and sticky notes in the workflow.
* It must start with a letter or an underscore (`_`).
* It can contain only letters, digits, underscores, and hyphens (`-`).
* It can't be a reserved name. Reserved names are built-in JavaScript object properties, such as `constructor`, `toString`, `hasOwnProperty`, `valueOf`, and `__proto__`.

| Valid | Invalid |
|---|---|
| `getOrder`, `_start`, `step-2`, `approval_1` | `2ndStep` (starts with a digit), `-step` (starts with a hyphen), `send email` (contains a space), `constructor` (reserved) |

The designer handles an identifier that's missing, invalid, reserved, or duplicated as follows:

* `addWorkflowItem` ignores the item and returns `false`.
* `importWorkflow` rejects the workflow with `IMPORT_INVALID_STRUCTURE`.
* `validateWorkflow` reports `ID_REQUIRED`, `ID_INVALID`, or `ID_DUPLICATE`.

## Triggers

Every workflow starts from a trigger. When a workflow has more than one trigger, choose the one to start from when you run it. The `runExecution` method takes the trigger identifier as its second argument.

### Manual trigger

`ManualTrigger` starts the run when the user clicks **Execute** or your code calls `runExecution`. It has no settings.

### Scheduled trigger

`ScheduledTrigger` runs the workflow on a schedule while the page is open.

| Setting | Description |
|---|---|
| `scheduleType` | `'Once'`, `'Recurring'`, or `'Cron'`. |
| `startDateTime` | When the schedule starts. |
| `interval`, `intervalUnit` | For `'Recurring'`: the repeat interval, with a unit of `'Minutes'`, `'Hours'`, `'Days'`, or `'Weeks'`. |
| `cronExpression` | For `'Cron'`: a five-field cron expression. |
| `endDateTime`, `maxOccurrences` | Optional limits. |
| `timeZone` | The IANA time zone that the schedule uses. |

N> A `'Once'` schedule runs only when you start it manually. `'Recurring'` and `'Cron'` schedules fire automatically after the run starts, and they keep firing until the run is stopped. Schedules run only while the browser tab is open.

### Webhook trigger

`WebhookTrigger` starts the run when your application passes a webhook message to the `triggerWebhook` method. For details, see [Webhook trigger](./webhook-trigger).

### Form input trigger

`FormInputTrigger` shows a form when the run starts and passes the submitted values to the workflow.

| Setting | Description |
|---|---|
| `formTitle` | The form title. |
| `fields` | The form fields. Each field has a `name`, `type`, `label`, `placeholder`, and `defaultValue`. Supported types are `'TextBox'`, `'TextArea'`, `'Number'`, `'Date'`, `'Password'`, `'Email'`, `'DropDown'`, `'CheckBoxes'`, and `'RadioButton'`. |

## Actions

### Set variable

`SetVariable` stores values in `$.variables` for later steps.

| Setting | Description |
|---|---|
| `variables` | A list of `{ variableName, value, valueType }` items. `valueType` is `'Auto'`, `'String'`, `'Number'`, `'Boolean'`, or `'Json'`. For arithmetic, set `isMathOperation: true` with `firstValue`, `operator`, and `secondValue`. |

### Filter

`Filter` keeps the items of a list that match the conditions.

| Setting | Description |
|---|---|
| `itemsPath` | An expression for the list, such as `$.previousStep.response.body.items`. |
| `conditions` | [Condition rules](#condition-rules) that are evaluated against each item. |
| `conditionLogic` | `'AND'` or `'OR'`. |

### Sort

`Sort` orders a list. Set `itemsPath`, `sortBy` (the field name), and `sortDirection` (`'Ascending'` or `'Descending'`).

### API request

`ApiRequest` sends an HTTP request from the browser.

| Setting | Description |
|---|---|
| `method` | `'GET'`, `'POST'`, `'PUT'`, `'PATCH'`, or `'DELETE'`. |
| `url` | An `http` or `https` URL. |
| `headers`, `queryParams` | Lists of `{ key, value }` pairs. |
| `bodyType`, `body` | `'Json'`, `'Text'`, or `'Expression'`. GET and DELETE requests don't send a body. |
| `authRef` | The name of a credential that's resolved through the `credentialRequested` event. |

The step's response contains `status`, `statusText`, `headers`, and `body`. A response status outside the 2xx range fails the step. Page cookies are never sent.

### Delay

`Delay` pauses the run.

| Setting | Description |
|---|---|
| `delayType` | `'Fixed'`, `'Until'`, or `'Dynamic'`. |
| `duration`, `timeUnit` | For `'Fixed'`. The unit is `'Seconds'`, `'Minutes'`, `'Hours'`, or `'Days'`. |
| `untilDateTime`, `timeZone` | For `'Until'`. |
| `dynamicCondition`, `dynamicConditionLogic`, `dynamicDuration`, `dynamicTimeUnit` | For `'Dynamic'`: the delay applies when the conditions match. |

### Formatter

`Formatter` converts data into readable output. Set `inputSource` to `'PreviousStep'` or `'ContextPath'` (with `sourcePath`), and set `format` to `'Text'`, `'JSON'`, `'Markdown'`, or `'Table'`.

### Custom

`Custom` hands its work to your application through the `customExecutionRequested` event. The `actionType` setting identifies the action. See [Custom steps](./host-integration#custom-steps).

## Flow

### Condition

`Condition` evaluates [condition rules](#condition-rules) and continues from its `true` or `false` port.

| Setting | Description |
|---|---|
| `conditions` | The condition rules. |
| `conditionLogic` | `'AND'` (default) or `'OR'`. |

### Switch

`Switch` continues from the first branch whose condition matches.

| Setting | Description |
|---|---|
| `branches` | A list of `{ branchName, condition }` items. Each branch name becomes an output port. |
| `defaultBranch` | The branch that's used when no condition matches. |

### Loop

`Loop` runs the steps connected to its `loop` port once for each item, and then continues from its `done` port.

| Setting | Description |
|---|---|
| `itemsPath` | An expression for the list. A single value is treated as a one-item list, and a missing value loops zero times. |
| `itemFields` | Optional field descriptions for the current item. The variable picker uses them. |
| `maxIterations` | The maximum number of iterations. |

Inside the loop, read the current item from `$.loop.currentItem` and its position from `$.loop.index`.

### End

`End` stops the current path and has no output ports. The execution log shows its `endMessage`.

## Human

### Approve

`Approve` pauses the run until a reviewer approves or declines, and then continues from its `approved` or `declined` port.

| Setting | Description |
|---|---|
| `reviewers` | A list of `{ type, value }` items. `type` is `'User'`, `'Role'`, `'Group'`, or `'Email'`. |
| `requestMessage` | The message shown to the reviewer. |
| `requestFields` | A list of `{ label, value }` items shown with the request. |

Handle the `approvalRequested` event to route the request to your approval system. When the event isn't handled, the designer shows a built-in approval dialog. See [Approvals](./host-integration#approvals).

### Notify

`Notify` sends a notification through your application. The `channel` setting determines which fields apply:

| Channel | Fields |
|---|---|
| `'Email'` | `to`, `cc`, `subject`, `message` |
| `'SMS'` | `phoneNumber`, `message` |
| `'Push'` | `deviceToken` or `userId`, `subject`, `message` |
| `'Teams'`, `'Slack'` | `channelId`, `mentionUsers`, `subject`, `message` |
| `'Webhook'` | `webhookUrl`, `method` (`'POST'` or `'PUT'`), `headers`, `body`, `message` |
| `'Custom'` | `target`, `subject`, `message` |

Handle the `notifyRequested` event to deliver the notification. See [Notifications](./host-integration#notifications).

## AI

`AI` sends a prompt to an AI provider and passes the answer to the next step.

| Setting | Description |
|---|---|
| `provider`, `model` | The provider (`'Gemini'`, `'OpenAI'`, or `'Claude'`) and the model name. |
| `credentialRef` | The name of a credential that's resolved through `credentialRequested`. Used when `credentialMode` is `'Reference'`. |
| `apiKey` | A raw key. It's available only when `credentialMode` is `'Inline'`. Use it for development only. |
| `serverUrl` | An optional backend that receives the request instead of the provider. |
| `systemPrompt`, `prompt` | The instructions and the prompt. Both accept expressions. |
| `outputFormat` | `'Text'`, `'JSON'`, or `'Markdown'`. |
| `responseFields` | For `'JSON'`: the expected fields, each with a `name`, `type`, and `description`. |
| `memoryEnabled`, `contextWindowLength`, `sessionKey` | Conversation memory, which is kept in session storage. |
| `timeoutSeconds` | The step timeout. |

## Condition rules

Condition, Filter, Delay, and Switch steps use condition rules:

| Property | Description |
|---|---|
| `field` | The value to test, usually an expression. |
| `valueType` | `'String'`, `'Number'`, `'Date'`, `'Boolean'`, or `'Array'`. |
| `operator` | One of the supported operators listed below. |
| `value` | The value to compare with. It isn't needed for `IsEmpty`, `IsNotEmpty`, `IsTrue`, or `IsFalse`. |

The supported operators are `Equal`, `NotEqual`, `Contains`, `NotContains`, `StartsWith`, `NotStartsWith`, `EndsWith`, `NotEndsWith`, `MatchesRegex`, `NotMatchesRegex`, `IsEmpty`, `IsNotEmpty`, `ArrayContains`, `ArrayNotContains`, `LengthEqual`, `LengthNotEqual`, `LengthGreaterThan`, `LengthLessThan`, `GreaterThan`, `GreaterThanOrEqual`, `LessThan`, `LessThanOrEqual`, `Before`, `BeforeOrEqual`, `After`, `AfterOrEqual`, `IsTrue`, and `IsFalse`.

## See also

* [Connections and ports](./connections)
* [Expressions and runtime context](./expressions)
* [Validation](./validation)
