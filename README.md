# AMQP.ServiceBus
There was a time that I badly need a service bus component which enables my UWP to create topic/subscription on AzureServiceBus. Unfortunately, neither Microsoft nor 3rd party component can do it for me. The Microsoft one requires full .Net environment and rejected UWP, the 3rd party library only has part of features I needed. So, I created this one based on the REST interface and customized for UWP project. To use Azure service bus, the service bus namespace and its access identity are required. This is a sample code demonstrating its usage.

    class sample
    {
        public static string ServiceNamespace
        {
            get { return "your-service-namespace"; }
        }

        public static string SASKeyName
        {
            get
            {
                return "RootManageSharedAccessKey";
            }
        }

        public static string SASKeyValue
        {
            get
            {
                return "key-for-read-and-write-access";
            }
        }

        public async Task TestAMQPService()
        {
            AMQPServiceProvider provider = AMQPServiceProviderFactory.CreateServiceProvider(
                ServiceProviderType.Azure,
                ServiceNamespace,
                SASKeyName,
                SASKeyValue);

            string topicName = "MyTopic";
            string queueName = "MyQueue";
            string subName = "MySubscription";

            // create topic and its subscription
            await provider.CreateQueue(topicName);
            await provider.CreateSubscription(topicName, subName);

            // send mesage to topic
            await provider.SendMessage(topicName, "test message");
            // read and delete the message from subscription.
            var topicMessage = await provider.ReceiveAndDeleteMessage(topicName, subName);
            // delete topic
            await provider.DeleteTopic(topicName);

            // create queue
            await provider.CreateTopic(queueName);
            // send message to queue;
            await provider.SendMessage(queueName, string.Empty);
            // read and delete the message from queue
            var queueMessage = await provider.ReceiveAndDeleteMessage(queueName, string.Empty);
            // delete queue
            await provider.DeleteQueue(queueName);



        }
    }