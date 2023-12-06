---
date: 2020-10-07
categories:
    - web
    - JavaScript
---

# Augmenting JS Functions to Self-Identify

Sometimes, if you are debugging in a legacy JS environment, you may not have just an easy time tracking down the source of a bug, or figuring out the exact order of events. This is an abominable trick I discovered on SO (possibly from this post). I was trying to find it again yesterday but I couldn’t get the Google search right, so it took forever, so I’m putting it here in the Mnemonic Debugger!

Anyway, without further ado I present to you:

function augment(withFn) {
    var name, fn;
    for (name in window) {
        fn = window[name];
        if (typeof fn === 'function') {
            window[name] = (function(name, fn) {
                var args = arguments;
                return function() {
                    withFn.apply(this, args);
                    return fn.apply(this, arguments);
                }
            })(name, fn);
        }
    }
}
augment(function(name, fn) {
    console.log("calling " + name);
});
Just plop it in at the end of your JS file and give it a go! Every time a function is called it should print its name to the dev console.