# isottope
Is an Event Loop for [otto](https://github.com/robertkrimen/otto) JavaScript engine, which itself is written in pure Go(ld).

Three JavaScript functions added; **subscribe**, **unsubscribe** and **emit**. Events can get triggered from inside both JavaScript and Go.

Not used heavily yet (just in one side project of mine), so any bug report is most appreciated.

From *examples* directory; you can define your own events and fire them, like this:

```go
func customEvent(vm *otto.Otto, loop *isottope.EventLoop) {
	loop.RegisterEvent(isottope.SimpleEvent(`TEST_OK`), true)

	vm.Run(`
		function target() {
			var args = ['input args:'];
			if (arguments.length > 0) {
				for (i = 0; i < arguments.length; i++) {
					args.push(arguments[i]);
				}
			}
			var msg = args.join(' ');
			console.log(msg);
		}

		subscribe('TEST_OK', target, 'DEFAULT-ARG-1', 'DEFAULT-ARG-2');

		emit('TEST_OK', "Custome Arg");	
		emit('TEST_OK');	
	`)

	loop.Emit(`TEST_OK`, `Another Custome Arg`)
	loop.Emit(`TEST_OK`)
}
```

Using timers is also possible; for example:

```go
func timer(vm *otto.Otto) {
	vm.Run(`
		var R = {};
		var c = 0;
		function target() {
			c++;
			console.log('registrar #' + c);
			if (c < 5) {
				return;
			}
			clearTimeout(R);
		}

		try {
			R = setInterval(target, 200);
		}
		catch(e) {
			console.error(e);
		}		
	`)
}
```

It's possible to implement more complicated event patterns by implementing `isottope.Event` interface - the timer itself is implemented using `isottope.Event` interface.