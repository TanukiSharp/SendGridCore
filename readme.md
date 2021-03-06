# MinimalSendGrid <a href="https://www.nuget.org/packages/MinimalSendGrid" target="_blank"><img src="https://img.shields.io/badge/nuget-1.1.0-blue.svg"></a>

Minimal implementation of the SendGrid HTTP v3 API to send emails, compatible with `.NET Platform Standard 1.0`.

# How to build

You can open the `MinimalSendGrid.sln` file in the root directory with Visual Studio 2015 Community Edition, and build directly from Visual Studio.

You can also build it with the command line, from the root directory:

    dotnet restore
    cd src\MinimalSendGrid
    dotnet build

and optionally build the tester application:

    cd ..\MinimalSendGrid.Tester
    dotnet build

# How to use

The very fist thing to do is to add a reference to `MinimalSendGrid` to your project, then add a `using` statement in your code.

    using MinimalSendGrid;

Use the `MessageBuilder` class to create a message.

    Message message = new MessageBuilder()
        .SetFrom("you@domain.ext")
        .AddTo("alice@another.domain.ext")
        .AddCc(new MessageEndPoint("bob", "bob@domain.ext"), "charly@domain.ext")
        .SetSubject("test subject")
        .AddBody("plain text test body")
        .Build();

Then instance an `IMessageSender` object, and send your message.

    var sender = new HttpV3MessageSender("<YOUR_API_KEY_HERE>");
    MessageSenderResult result = await sender.Send(message);
    Console.WriteLine(result);

That's it, enjoy!

# Remarks

In version `1.1.0` the API changed, causing a breaking change compared to version `1.0.1` and and lower.
The `MessageBuilder.SetBody` method has been renamed to `MessageBuilder.AddBody`, since it is now possible to add several bodies, such as plain text and/or HTML.

The `HttpV3MessageSender` class is stateless, meaning that you can call the `Send` method several times in a row before awaiting for responses, however the `Send` method is not thread-safe.
So, in multi-threading scenario, since you cannot lock around an `await` statement, it is better to instance a `HttpV3MessageSender` object per thread.
