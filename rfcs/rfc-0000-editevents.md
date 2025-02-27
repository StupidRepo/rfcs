- Feature Name: Message Editing Events
- Start Date: (27/02/2025)
- RFC PR: None
- Tracking Issue: None
- Status: draft

# Summary
This change adds 2 server events and 2 client events for message editing. Just like how typing in the message box on a Revolt client sends typing events, editing a message and typing in the edit box will send events for begin/end typing.

# Motivation
Sometimes you may make a mistake in a message when having a conversation with someone. If you've typed a confusing sentence, the other person might try to understand it whilst you're editing the message to make sense. This way, if you type something confusing and hit send but then go back to edit it, the other person knows to wait until you've edited the message to try and understand what you're saying.

Overall, it's just to stop confusion and to allow others to know when messages are being edited.

# Guide-level explanation
This RFC adds events which should be used to indicate to the server when the edit message box of a message is being typed in or not typed in. It should behave exactly the same as a channel typing/begin typing event (same delays in sending, etc.) except only occur when, like I said, a message edit box is/is not being typed in.

# Reference-level explanation
This RFC, as previously mentioned, adds 2 server->client and client->server events. These events are:
- S2C:
    - `MessageStartEditing`
        - `id` - The ID of the message being edited
        - `channel` - The channel of where the message is
        - `user` - The user editing the message
    - `MessageStopEditing`
        - `id` - The ID of the message that is no longer being edited
        - `channel` - The channel of where the message is
        - `user` - The user who previously was editing the message
- C2S:
    - `BeginEditing`
        - `id` - The ID of the message being edited
        - `channel` - The channel of where the message is
    - `StopEditing`
        - `id` - The ID of the message that is no longer being edited
        - `channel` - The channel of where the message is

# Drawbacks
I see no drawbacks to this.

# Rationale and alternatives
Revolt clients should implement this by showing some kind of pencil icon or "(editing...)" message in the message box itself.

## **Why is this design the best?**
This design is the best because there are already existing events for typing a new message, so why not have events for editing a new message?
It is literally just taking a featue that already exists and then editing it slightly for another purpose, however it still has all the same reasonings as the feature it was taken from.

My point, somewhat, is this: If you disagree with having editing events, then why have typing events?

# Prior art
- WhatsApp & Telegram - Both WhatsApp and Telegram sends a lot of events based on different interactions. Some examples are below. One of these, however, is an editing message event which shows "editing message..." under the contact's name in the chat view.
    - "recording voice message..." (Both, iirc)
    - "uploading video..." (Both, iirc)
    - "recording video clip..." (WhatsApp)
    - "uploading file..." (Telegram)

# Unresolved questions
N/A

# Security concerns
If you were to pass through a message ID that wasn't your own message, the relevant S2C event would be sent to other clients with your user ID and the target message ID that isn't authored by you.

Considering the future idea below, the relevant C2S event should be checked on the server to see if the user has permissions to edit that target message before sending the relevant S2C event. Whilst not required (because it should've been done on the server-side) the relevant S2C event could also be checked on the client to make sure not to trigger any action (like showing the pencil icon/editing text) if the message is not allowed to be edited by the received user ID.

Of course the future idea below is just an idea and, if it is not implemented, checking if the user is the author of the message (on server and optionally the client) would be sufficient enough.

# Future ideas
- If there was, for some reason, a featue for editing other people's messages (with relevant permissions), you could also show a mini-profile picture of the person editing a message in the message box. That is why a user ID is passed through with the S2C event.