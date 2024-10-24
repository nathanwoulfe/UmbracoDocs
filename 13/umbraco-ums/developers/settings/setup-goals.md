---
icon: square-exclamation
description: >-
  Goals are the most important part of uMS. Without goals, personalization, A/B
  testing, and tracking analytics make no sense.
---

# Set up Goals

Personalization is never the goal; the goal is to increase your goals which can be achieved by personalization or A/B testing.

You can set up goals in the Goals menu and specify their value.

You have a complete overview of all of the goals that are currently set:

![Find the Settings dashboard in the Enage section of the backoffice.](../../.gitbook/assets/engage-settings-overview-of-goals.png)

From this page, you can edit existing goals or set up new goals.

## Setting up a new goal

When you click on **Create new goal** you can set up a new goal. You have to give it a name and an optional goal value.

You can specify whether it is a micro or macro goal.

* A macro goal should be used for the "bigger things" in your website like a purchase or filling in a contact form.
* Micro goals are smaller / less important things like "_Reaching the contact page_" or "_Clicking open a FAQ_". These micro goals should eventually contribute to macro goals.

In the Umbraco uMS, we always keep track of the macro goals, for example during A/B tests. Perhaps your micro goal is increased, but it hurts your macro goal. In that case, Umbraco uMS will give you a warning.

You can specify whether you want to increase or decrease the goal. Most of the time you want to increase your goal. Other times you might want to lower the goal. An example of this could be a goal of "_Unsubscribing from a newsletter_"

You can specify how the goal will be triggered:

* Via a pageview/set of pages that are visited
* Via a page event that can be triggered
* Via an Umbraco Forms submission
* Via some custom code

![Create a new goal by giving it a name, setting a value and deciding on a set of parameters.](../../.gitbook/assets/engage-settings-setup-new-goal.png)
