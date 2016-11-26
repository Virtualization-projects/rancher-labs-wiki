Not all logic for a resource needs to take place within the confines of the cattle server or even the agents we've written.

Users can add custom logic to any resource process by writing an external event handler. In short, an external event handler let's you subscribe to a feed of events for a particular set of processes and handle them as you see fit. It is completely decoupled from the cattle codebase, but it allows users to plug into resource life-cycles in the exact same way.

There is a great example written in bash here:

[https://github.com/rancherio/cattle/blob/master/docs/examples/handler-bash/simple_handler.sh](https://github.com/rancherio/cattle/blob/master/docs/examples/handler-bash/simple_handler.sh)

> The above mentioned script doesn't exist anymore in the latest tree. But if you want to take a peek at the last available version, here it is: [simple_handler.sh](https://github.com/rancher/cattle/blob/e85559b81b27a2f942579888c300d62742b3aa6f/docs/examples/handler-bash/simple_handler.sh)

Can you adapt this to interact with your animal or pet resource? Give it a shot.


**Next:** [[Cowpoke 3.5: Interact with a resource in an existing agent|Cowpoke 3.5: Interact with a resource in an existing agent]]