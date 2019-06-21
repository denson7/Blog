
## 使用console
```
// The simple case. Use this instead of alert():
console.log('This is a console message!');

// It supports embedding of variables as well:
var a = 'morning', b = 'Miss';
console.log('Good %s %s! How are you feeling today?', a, b);

// Interactively browse the properties of a method (similar to console.log):
console.dir(window);

// Information message (in webkit it looks like console.log)
console.info('Everything is OK');

// Warning message
console.warn('Something may be wrong');

// Error message (will print a stack trace)
console.error('Ooops. That was bad.');

// Counting things
for (var i = 0; i < 20; i++) {
    console.count('Counter Name');
}

// Starts a collapsable group of log messages
console.group("Preflight check");
console.info('Fuel is OK');
console.info('Temperature is normal');
console.error('Wings are missing');
console.groupEnd();

// Timing things
console.time('The million-dollar loop')

var dollars = 0;

for (var i = 0; i < 100000; i++) {
    dollars += 10;
}

console.timeEnd('The million-dollar loop');//The million-dollar loop: 4.648ms

// Profiling code (it will show up in your console's Profile tab)
console.profile('My app performance');

var arr = [];
$.each([0, 1, 2, 3, 4, 5, 6, 7, 8, 9], function () {
    arr.push(this + 1);
});

console.profileEnd('My app performance');
//Profile 'My app performance' started.
//Profile 'My app performance' finished.
```
