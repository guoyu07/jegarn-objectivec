# Jegarn iOS SDK

Objective-C implementation of jegarn client.




## Install

    pod "JegarnClient"




## Example

For more details, please see the demo project.
And these are some base usages.

connect

    JegarnClient * client = [[JegarnClient alloc] init];
    client.account = @"test";
    client.password = @"test";
    client.host = @"jegarn.com";
    client.port = 7773;
    client.reconnectInterval = 30.0;
    client.listener = [[JegarnListenerExample alloc] init];
    client.runLoop = [NSRunLoop currentRunLoop];
    client.runLoopMode = NSDefaultRunLoopMode;
    [client connect];

connect to ssl server

    JegarnSecurityPolicy *securityPolicy = [JegarnSecurityPolicy policyWithPinningMode:JegarnSSLPinningModeCertificate];
    NSString *certificate = [[NSBundle bundleForClass:[self class]] pathForResource:@"server" ofType:@"cer"];
    securityPolicy.pinnedCertificates = @[[NSData dataWithContentsOfFile:certificate]];
    securityPolicy.allowInvalidCertificates = NO;
    securityPolicy.validatesCertificateChain = NO;

    NSString *p12File = [[NSBundle mainBundle] pathForResource:@"client" ofType:@"p12"];
    NSString *p12Password = @"111111";
    NSArray *certificates = [JegarnSecurityPolicy clientCertsFromP12:p12File passphrase:p12Password];

    JegarnClient * client = [[JegarnClient alloc] init];
    client.account = @"test";
    client.password = @"test";
    client.host = @"jegarn.com";
    client.port = 7773;
    client.reconnectInterval = 30.0;
    client.listener = [[JegarnListenerExample alloc] init];
    client.runLoop = [NSRunLoop currentRunLoop];
    client.runLoopMode = NSDefaultRunLoopMode;
    client.enableSsl = true;
    client.securityPolicy = securityPolicy;
    client.certificates = certificates;
    [client connect];

and new message listener of chat packet

    - (void)viewDidAppear:(BOOL)animated {
        [[JegarnChatPacketManager sharedInstance] addDelegate:self];
        [super viewDidAppear:animated];
    }

    - (void)viewDidDisappear:(BOOL)animated {
        [[JegarnChatPacketManager sharedInstance] removeDelegate:self];
        [super viewDidDisappear:animated];
    }

    - (BOOL)processPacket:(JegarnChatPacket *)packet {
        if([packet isKindOfClass:[JegarnTextChatPacket class]]){
            NSString *fromUid = packet.from;
            NSString *toUid = packet.to;
            if ([fromUid isEqualToString:_chatWithUser.uid] && [toUid isEqualToString:_loginUser.uid]) {
                NSString *content = ((JegarnTextChatPacket *) packet).content.text;
                [self appendUser:_chatWithUser textMessage:content];
            }
        }
        return YES;
    }

send new message

    JegarnTextChatPacket * packet = [JegarnTextChatPacket initWithFrom:@"my_uid" to:@"friend_uid" text:@"hello"];
    [client sendPacket:packet];



## License

[Apache License Version 2.0](./LICENSE)

