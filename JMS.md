

https://blogs.sap.com/2017/06/19/cloud-integration-configure-asynchronous-messaging-with-retry-using-jms-adapter/

https://blogs.sap.com/2017/07/17/cloud-integration-how-to-configure-session-handling-in-integration-flow/

https://blogs.sap.com/2017/10/04/cloud-integration-jms-resource-and-size-limits-in-cpi-enterprise-edition/

Size Limits for Messages, Headers, Properties and Attachments
The following size limits apply when saving messages to JMS queues.

There are no size limits for the payload. The message is split internally when it is put into the queue
There are no size limits for the attachements. The message and attachment are split internally when put into the queue
Headers and exchange properties defined in the integration flow before the message is saved to queue must not exceed 4MB for headers and properties together.
Important: As mentioned, there are no hard size limits for payload and attachments, but you need to keep in mind that processing the message in the CPI runtime may limit the possible size of payload and attachments for your scenario. You as scenario developer have to test and restrict the limits your scenario can handle!


Configure the JMS Sender Channel
Create the integration flow with the outbound channel required by your scenario, and use the JMS adapter as the inbound adapter. You only have to configure the Queue Name for the JMS queue that you want to poll the messages from. Use the same queue name used in the receiving integration flow.

Number of Concurrent Processes is set to 1 per default and should only be increased if more parallel processing is needed. Because the JMS resources are limited, you need to keep the number of parallel processes as low as possible. Especially if large messages are processed in the scenario, increasing the parallelism may lead to out of memory problems.

In the JMS sender channel you also have to configure the retry handling. Set the Retry Interval as required for your scenario (, the default value is 1 minute). We also recommend using Exponential Backoff, which means that the retry interval is doubled after each unsuccessful retry. By using this setting you can avoid calling the receiver backend every minute if it is unavailable for a longer time period. The Maximum Retry Interval defines the maximum time between two retries if exponential backoff is used. The recommendation is to either keep the 60 minutes or even increase it if this is acceptable by the scenario.

Note, that the JMS sender does not guarantee the order in which the messages are consumed. If several processes are configured to consume messages and/or multiple worker nodes process messages, the messages are processed in parallel. Furthermore, in case the message is in retry because of an error it is taken out from processing until the retry interval is reached.




Configure Dead Letter Handling in JMS Sender Channel
In the JMS sender adapter, the dead letter queue can be configured as of JMS adapter version 1.1. The checkbox, Dead-Letter Queue, for activating the dead letter queue is available on the Connection tab. The checkbox is selected by default, meaning that dead letter handling is active. After the second retry of a message that could not be completed because of a node crash, this message is moved to a dead letter queue and not processed any further. The number of retries is not configurable.

Performance Impact of the Configuration
The Dead-Letter Queue handling is a performance intensive operation, so you should expect a significant impact on the performance. This impact is even higher if you run high-load scenarios. This is because the Dead-Letter Queue handling is based on the database and the database does not scale as good as JMS.

For high-load scenarios or if you are sure that only small messages will be processed in your scenario, you should deselect the checkbox to improve the performance, but do keep the risk of an outage in mind. The recommended configuration would be to configure the size check in the used sender adapter and with this configuration reject large messages to avoid that a large message can cause an out-of-memory. Unfortunately not all sender adapters support the size check yet.
