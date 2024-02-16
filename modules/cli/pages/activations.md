= Activations

In the preceding paragraph, we saw when we invoke an action without waiting for the result; we receive as an answer just an invocation id.

This fact brings us to the argument of this paragraph: the subcommand `wsk activation` to manage the results of invocations.


To explore it, let's create a simple `echo.js` file:


.A simple echo with logging for testing activations
----
function main(args) {
  console.log(args)
  return args
}
----

Now, let's deploy and invoke it (with a parameter `hello=world`) to get the activation id:

----
$ wsk action create basics/echo echo.js
ok: created action basics/echo
$ wsk action invoke basics/echo -p hello world
ok: invoked /_/basics/echo with id 82deb0ec37524a9e9eb0ec37525a9ef1
----


As we explained in Chapter 1, when actions are invoked, they are identified by an activation id, used to save and retrieve results and logs from the database.

Now, the long alphanumeric (actually, hexadecimal) identifier displayed is the activation id. We can now use with it the option `result` to get the result, and `logs` to get the logs.

----
$ ID=$(wsk action invoke basics/echo -p hello world \
  | awk '{ print $6}')
$ wsk activation result $ID
{
    "hello": "world"
}
$ wsk activation logs $ID
2018-03-15T18:17:36.551486467Z stdout: { hello: 'world' }
----

[TIP]
You can also use the `wsk result --last` to get the result of the last invoked action.

The `activation` subcommand has another two useful options. 

One is `list`. It will return a list of *all* the activations in chronological order. Since the list can be very long, it is useful always to use the option `--limit <n>` to see only the latest `<n>`:

----
$ wsk activation list --limit 4
activations
82deb0ec37524a9e9eb0ec37525a9ef1 echo
219bacbdb838449d9bacbdb838149de2 echo
1b75cd02fd1f4782b5cd02fd1f078284 echo
6fa117115aa74f90a117115aa7cf90e0 now
----

Another useful option (and probably the most useful)  is `poll`. With this option, you can continuously display logs for actions as they happen.  It is a handy option for debugging because let you to monitor what is going on in the remote serverless system.

[TIP]
You can poll for just an action or for all the actions at the same time.