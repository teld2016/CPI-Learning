
Size Limits for Messages, Headers, Properties and Attachments
The following size limits apply when saving messages to JMS queues.

There are no size limits for the payload. The message is split internally when it is put into the queue
There are no size limits for the attachements. The message and attachment are split internally when put into the queue
Headers and exchange properties defined in the integration flow before the message is saved to queue must not exceed 4MB for headers and properties together.
Important: As mentioned, there are no hard size limits for payload and attachments, but you need to keep in mind that processing the message in the CPI runtime may limit the possible size of payload and attachments for your scenario. You as scenario developer have to test and restrict the limits your scenario can handle!
