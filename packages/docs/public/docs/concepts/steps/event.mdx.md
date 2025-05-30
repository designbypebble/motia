---
title: Event Step
---

The **Event Step** lets you define custom logic in response to subscribed events and at the same time trigger other steps by emitting new events. It enables communication between different parts of your flow.

## Config

The following properties are specific to the Event Step, in addition to the [common step config](/docs/concepts/steps/defining-steps#config).

<DescriptionTable
  type={{
    input: {
      description:
        'This is used for input validation. For TypeScript/JavaScript steps, it uses zod schemas. For Python steps, it uses Pydantic models. This validates the input before executing the step handler.',
      type: 'string[]',
    },
  }}
/>

The following examples showcase how to configure an **Event Step**

<Tabs  items={['TS', 'JS', 'Python']}>
  <Tab value="TS">
    ```typescript
    import { EventConfig, Handlers } from 'motia'
    import { z } from 'zod'

    export const config: EventConfig = {
      type: 'event',
      name: 'stepA',
      description: 'Hello from Step A',
      subscribes: ['pms.start'],
      emits: ['pms.stepA.done'],
      input: z.object({ message: z.string() }),
      flows: ['parallel-merge'],
    }

    export const handler: Handlers['stepA'] = async (input, { emit, logger }) => {
      logger.info('Processing message:', input.message)

      await emit({
        topic: 'pms.stepA.done',
        data: {
          result: `Processed: ${input.message}`
        }
      })
    }
    ```
  </Tab>
  <Tab value="JS">
    ```javascript
    const z = require('zod');

    const config = {
      type: 'event',
      name: 'stepA',
      description: 'Hello from Step A',
      subscribes: ['pms.start'],
      emits: ['pms.stepA.done'],
      input: z.object({ message: z.string() });,
      flows: ['parallel-merge'],
    };

    const handler = async (input, { emit, logger }) => {
      logger.info('Processing message:', input.message)

      await emit({
        topic: 'pms.stepA.done',
        data: {
          result: `Processed: ${input.message}`
        }
      })
    };

    module.exports = { config, handler };
    ```
  </Tab>
  <Tab value="Python">
    ```python
    config = {
      "type": "event",
      "name": "Call OpenAI",
      "subscribes": ["call-openai"], 
      "emits": ["openai-response"],
      "input": {
        "type": "object",
        "properties": { "type": "string" },
      },
      "flows": ["openai"]
    }

    async def handler(input, context):
      context.logger.info("Processing input:", { "input": input })

      await context.emit({
        "topic": "openai-response",
        "data": {
          "result": f"Processed: {input.get("message", "")}"
        }
      })
    ```
  </Tab>
</Tabs>

## Example
