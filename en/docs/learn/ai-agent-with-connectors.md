# Integrating agents with external endpoints using prebuilt connectors

In this tutorial, you’ll create an AI-powered personal assistant agent that integrates with Gmail and Google Calendar to help you efficiently manage emails, tasks, and schedules. You'll use the prebuilt Ballerina Integrator (BI) connectors for seamless integration by turning their actions into agent tools.

## Prerequisites

To get started, you’ll need to configure Google API credentials:

1. Go to the [Google Cloud Console](https://console.cloud.google.com/) and sign in.
2. Follow [this guide](https://developers.google.com/identity/protocols/oauth2) to generate your **Client ID**, **Client Secret**, and **Refresh Token**.
3. Make sure the necessary scopes and permissions are enabled for both the Gmail and Calendar APIs.

##  Create the agent

Before adding tools, make sure you’ve set up your agent by completing steps 1 to 5 in the [Agent Integration Guide](https://bi.docs.wso2.com/learn/ai-agent-integration/). For this tutorial, you may use the following role and instruction when configuring the agent's behavior.

**Role**
``` md
Personal AI Assistant
```

**Instructions**
``` md
You are Nova, a smart AI assistant helping me stay organized and efficient.

Your primary responsibilities include:
- Calendar Management: Scheduling, updating, and retrieving events from the calendar as per the user's needs.
- Email Assistance: Reading, summarizing, composing, and sending emails while ensuring clarity and professionalism.
- Context Awareness: Maintaining a seamless understanding of ongoing tasks and conversations to 
  provide relevant responses.
- Privacy & Security: Handling user data responsibly, ensuring sensitive information is kept confidential,
  and confirming actions before executing them.

Guidelines:
- Respond in a natural, friendly, and professional tone.
- Always confirm before making changes to the user's calendar or sending emails.
- Provide concise summaries when retrieving information unless the user requests details.
- Prioritize clarity, efficiency, and user convenience in all tasks.
```

## Use connector actions as agent tools

BI includes prebuilt connectors for many external services like Gmail and Google Calendar. You can directly use their actions as tools for your agent—no need to write custom integration code. This significantly reduces the manual effort typically required when working with external APIs.


### Add gmail tools to the agent

#### Tool 1: List unread emails

##### Step 1: Add the gmail connector
1. In Agent Flow View, click the **`+`** button at the bottom-left of the `AI Agent` box.
2. Click the **`+`** button next to **`Tools`** → **`Create New Tool`**.
3. Click **`Add Connection`** under the **Connections** section.
4. Search for and select the **Gmail** connector.

##### Step 2: Configure the gmail connector
1. In the configuration panel:
   - Click **`Config`** to open the **`Expression Helper`**.
   - Under the **`Construct Record`** tab, select **`ConnectionConfig`**.
   - Set the `auth` type to **`OAuth2RefreshTokenGrantType`**.
   - Fill in your **`clientId`**, **`clientSecret`**, and **`refreshToken`**.

    !!! note
        Externalize credentials using configurable values to avoid exposing them in your version control system. See [Configurations]({{base_path}}/get-started/key-concepts/#configurations) for more details.

2. Save the configuration. You’ll now see the Gmail connection listed under **`Connections`**.

##### Step 3: Create the tool
1. Select the Gmail connection → choose the action **`List messages in user’s mailbox`**.
2. Provide the required **`Tool Name`** input as `listUnreadEmails`, and optionally add a meaningful **`Description`** to help the LLM better understand the tool's purpose.

##### Step 4: Customize the tool
1. Click on the circular `listUnreadEmails` tool node.
2. Click **`⋮` > `View`** to open the tool function.
3. Click the **`Gmail connector action node`** (the rectangle connected to the Gmail connection) to open the configuration panel for that specific connector action.
4. Update these inputs:
   - Set **`userId`** to **`"me"`**. The value `"me"` represents the authenticated user.
   - Under **`Advanced Configurations`**, set the **`q`** input to `"is:unread"` to filter unread emails only.
5. Click **`Save`**.

##### Step 5: Clean up
1. Remove the `userId` parameter from the function as it is no longer used in the tool:
   - Click **`Edit`** in the top-right of the function panel.
   - Click the **`Trash`** icon next to `userId`.
   - Click **`Save`**.

You’ve now created a tool that lists unread emails in the user’s Gmail inbox.

#### Tool 2: Read a specific email

##### Step 1: Create the tool
1. In Agent Flow View, click **`+`** under **Tools** → **Create New Tool**.
2. Select the existing **`gmailClient`** connection.
3. Choose the action **`Gets the specified message`**.
4. Name the tool as `readSpecificEmail` and optionally add a description.

##### Step 2: Customize the tool
1. Open the `readSpecificEmail` tool node → **`⋮` > `View`**.
2. Click the Gmail action node and update inputs:
- Set **`userId`** to **`"me"`**. The value `"me"` represents the authenticated user.
- Under **`Advanced Configurations`**, set the **`format`** input to **`"full"`** to get the full email message data with the body content parsed.
3. Click **`Save`**.

##### Step 3: Clean up
Remove `userId` from parameters (as done previously) and save the tool.

#### Tool 3: Send an email

##### Step 1: Create the tool
1. Use the existing **`gmailClient`** connection.
2. Select the action **`Sends the specified message to the recipients`**.
3. Name the tool as `sendEmail` and optionally add a helpful description.

##### Step 2: Customize and clean up
1. Set `userId` to **`"me"`** in the connector action configuration (as done previously) .
2. Remove `userId` from the parameters.
3. Save your tool.

### Add calendar tools to the agent

#### Tool 4: List calendar events

##### Step 1: Add the google calendar connector
1. In Agent Flow View, click the **`+`** button at the bottom-left of the `AI Agent` box.
2. Click the **`+`** button next to **`Tools`** → **`Create New Tool`**.
3. Click **`+`** button of the **Connections** section.
4. Search for and select the **Gcalendar** connector.

##### Step 2: Configure the google calendar connector
1. In the configuration panel:
   - Click **`Config`** to open the **Expression Helper**.
   - Under the **Construct Record** tab, select **`ConnectionConfig`**.
   - Set the `auth` type to **OAuth2RefreshTokenGrantType**.
   - Fill in your **clientId**, **clientSecret**, and **refreshToken**.

    !!! note
        Externalize credentials using configurable values to avoid exposing them in your version control system. See [Configurations]({{base_path}}/get-started/key-concepts/#configurations) for more details.

2. Save the configuration. You’ll now see the Google calendar connection listed under **Connections**.

##### Step 3: Create the tool
1. Select the Google calendar connection → choose the action **`Returns events on the specified calendar.`**.
2. Provide the required **Tool Name** input as `listCalendarEvents`, and optionally add a meaningful **Description**.

##### Step 4: Customize the tool
1. Click on the circular `listCalendarEvents` tool node.
2. Click **⋮ > View** to open the tool function.
3. Click the **Google calendar connector action node** (the rectangle connected to the Google calendar connection) to open the configuration panel for that specific connector action.
4. Update the `calendarId` input to `"primary"` which allows access to the primary calendar of the authenticated user.
 5. Click **Save**.

##### Step 5: Clean up
1. Remove the `calendarId` parameter from the function as it is no longer used in the tool:
   - Click **Edit** in the top-right of the function panel.
   - Click the **Trash** icon next to `calendarId`.
   - Click **Save**.

#### Tool 5: Create calendar event

##### Step 1: Create the tool
1. In Agent Flow View, click **`+`** under **Tools** → **Create New Tool**.
2. Select the existing **`gcalendarClient`** connection.
3. Choose the action **`Creates an event`**.
4. Name the tool as `createCalendarEvent` and optionally add a helpful description.

##### Step 2: Customize the tool
1. Click on the circular `createCalendarEvent` tool node.
2. Click **⋮ > View** to open the tool function.
3. Click the **Google calendar connector action node** to open the configuration panel for that specific connector action.
4. Update the `calendarId` input to `"primary"`.
5. Click **Save**.

##### Step 3: Clean up
1. Remove the `calendarId` parameter from the function as it is no longer used in the tool:
   - Click **Edit** in the top-right of the function panel.
   - Click the **Trash** icon next to `calendarId`.
   - Click **Save**.

## Interact with the agent

After completing the above steps, your personal AI assistant agent is now ready to assist you with necessary tasks. Ballerina Integrator provides a built-in chat interface to interact with the agent.

To start chatting with the agent:

1. Click the **`Chat`** button located at the top-left corner of the interface.
2. You will be prompted to run the integration. Click **`Run Integration`**.
3. If you have added any variables to the project, you’ll be prompted to update their values in the Config.toml file. Configure them to continue with the execution of the agent.
4. Start chatting with your assistant.
