# flowdock bb tool integration

We use a CLI to deploy, restart or get an overview of our queues:

    bb queues top
    bb deploy briefkasten
    bb restart fbnot

The goal was to allow us doing all this from our mobile clients
without always having your computer with you during pager-duty to do
small maintenance jobs.

Inspired by message passing from recent Elixir talks, I use shell
pipes to pass messages between processes. This example is a bit
contrived, but fun.

## flowdock_stream

This tool listenes on a particular flowdock stream with a defined
regular expression to catch our `bb` invocations and ouputs the json
to the shell, where it can be picked up by another piped command.

## shell_command

This is an example of how we process data, transform it with the
outcome of the shell command and pay it forward.

## flowdock_response

This sends data back to the particular bb tool invocation back in the
chat, by receiving data from the `shell_command` and the `thread_id` from the
`flowdock_stream`.

# Example

    # flowdock_bb.sh
    export flowdock_token=YOUR_TOKEN
    export flowdock_flow=YOUR_FLOW
    export flowdock_regexp="^bb "
    export flowdock_organization=YOUR_ORGANIZATION

    ./bin/flowdock_stream | ./bin/shell_command | ./flowdock_response \
    | jq "." >> /var/log/flowdock_bb_invocations.log

# TODO

* add wrapper script for bastion server
* make sure it's always running
