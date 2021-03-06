#### 12. Angular
#### HttpClient & RxJS basics


---

### RxJS
#### Reactive Extensions


---

#### Purpose of this lecture

* To grasp some overall RxJS concepts and to be able to use Angulars HttpClient in a basic way.
* Not to be a RxJS pro, we could have spent weeks expoloring RxJS itself.


---

#### RxJS

* Reactive Extensions library for JavaScript
* Is standalone library which Angular uses.
* RxJS is a dependency, check package.json.
* <a href="https://rxjs-dev.firebaseapp.com/guide/overview" target="_blank">RxJS Documentation</a>


---

"RxJS is a library for composing asynchronous and event-based programs by using observable sequences." - <a href="https://rxjs-dev.firebaseapp.com/guide/overview" target="_blank">RxJS Documentation</a>


---

<img src="/media/angular-images/angular-12/pushpull2.png" alt="push and pull">


---

#### What is Pull?

* "In Pull systems, the Consumer determines when it receives data from the data Producer. The Producer itself is unaware of when the data will be delivered to the Consumer."  - <a href="https://rxjs-dev.firebaseapp.com/guide/observable">RxJSDocumentation</a>
* "Every JavaScript Function is a Pull system. The function is a Producer of data, and the code that calls the function is consuming it by "pulling" out a single return value from its call." - <a href="https://rxjs-dev.firebaseapp.com/guide/observable">RxJSDocumentation</a>


---

#### What is Push?

* "What is Push? In Push systems, the Producer determines when to send data to the Consumer. The Consumer is unaware of when it will receive that data." - <a href="https://rxjs-dev.firebaseapp.com/guide/observable">RxJSDocumentation</a>
* "Promises are the most common type of Push system in JavaScript today. A Promise (the Producer) delivers a resolved value to registered callbacks (the Consumers), but unlike functions, it is the Promise which is in charge of determining precisely when that value is "pushed" to the callbacks." - <a href="https://rxjs-dev.firebaseapp.com/guide/observable">RxJSDocumentation</a>


---

#### Essential concepts

* Observable: represents the idea of an invokable collection of future values or events.
* Observer: is a collection of callbacks that knows how to listen to values delivered by the Observable.
* Subscription: represents the execution of an Observable, is primarily useful for cancelling the execution.
* Operators: are pure functions that enable a functional programming style of dealing with collections with operations like map, filter, concat, flatMap, etc.
* Subject: is the equivalent to an EventEmitter, and the only way of multicasting a value or event to multiple Observers.
* <a href="http://reactivex.io/rxjs/manual/overview.html#introduction">RxJSDocumentation</a>


---

#### Observables
RxJS introduces Observables, a new Push system for JavaScript. An Observable is a Producer of multiple values, "pushing" them to Observers (Consumers).

* A Function is a lazily evaluated computation that synchronously returns a single value on invocation.
* A generator is a lazily evaluated computation that synchronously returns zero to (potentially) infinite values on iteration.
* A Promise is a computation that may (or may not) eventually return a single value.
* An Observable is a lazily evaluated computation that can synchronously or asynchronously return zero to (potentially) infinite values from the time it's invoked onwards.



---

### Declaring an Observable
```JavaScript
var observable = Rx.Observable.create(function (observer) {
  observer.next(1);
  observer.next(2);
  observer.next(3);
  setTimeout(() => {
    observer.next(4);
    observer.complete();
  }, 1000);
});
```


---

### Eventlisteners & Observables
```JavaScript
var button = document.querySelector('button');
button.addEventListener('click', () =>    console.log(’Clicked!’)
);
```

Using RxJS you create an observable instead.
```JavaScript
var button = document.querySelector('button');

Rx.Observable.fromEvent(button, 'click')
  .subscribe(() => console.log('Clicked!'));
```


---

<img src="/media/angular-images/angular-12/promisevsobs.png" alt="observable vs promise">


---

### Observer

An Observer is a consumer of values delivered by an Observable. Observers are simply a set of callbacks, one for each type of notification delivered by the Observable: next, error, and complete.

```JavaScript
var observer = {
  next: x => console.log('Observer got a next value: ' + x),
  error: err => console.error('Observer got an error: ' + err),
  complete: () => console.log('Observer got a complete notification'),
};
```
Observers are just objects with three callbacks, one for each type of notification that an Observable may deliver.
<a href="http://reactivex.io/rxjs/manual/overview.html#observer">RxJSDocumentation</a>


---


### Subscribing

Subscribing to an Observable is like calling a function, providing callbacks where the data will be delivered to.

A Subscription essentially just has an unsubscribe() function to release resources or cancel Observable executions.
```JavaScript
var observable = Rx.Observable.interval(1000);
var subscription = observable.subscribe(x => console.log(x));
// Later:
// This cancels the ongoing Observable execution which
// was started by calling subscribe with an Observer.
subscription.unsubscribe();
```
<a href="http://reactivex.io/rxjs/manual/overview.html#subscription">RxJSDocumentation</a>


---

### Subjects
An RxJS Subject is a special type of Observable that allows values to be multicasted to many Observers.

While plain Observables are unicast (each subscribed Observer owns an independent execution of the Observable), Subjects are multicast.

Subjects are like EventEmitters: they maintain a registry of many listeners.


---

```JavaScript
var subject = new Rx.Subject();

subject.subscribe({
  next: (v) => console.log('observerA: ' + v)
});
subject.subscribe({
  next: (v) => console.log('observerB: ' + v)
});

subject.next(1);
subject.next(2);
```


---

#### Operators

* Most operators operate on an Observable and return an Observable. This allows you to apply these operators one after the other, in a chain.
* <a href="http://reactivex.io/documentation/operators.html">Complete list</a>


---

#### Observables in Angular

* The EventEmitter class extends Observable.
* The HTTP module uses observables to handle AJAX requests and responses.
* The Router and Forms modules use observables to listen for and respond to user-input events.


---

#### HttpClient
Angular’s HttpClient returns observables from HTTP method calls.

* HTTP requests are cancellable through the unsubscribe() method.
* Requests can be configured to get progress event updates.
* Failed requests can be retried easily.


---

####  HttpClientModule
```JavaScript
import { HttpClientModule } from '@angular/common/http';

imports: [
  BrowserModule,
  AppRoutingModule,
  FormsModule,
  HttpClientModule
]
```


---

#### Angular HttpClient

* Simplified client HTTP API for Angular applications that rests on the XMLHttpRequest interface exposed by browsers.

* Observable API's, and streamlined error handling.


---

#### HttpClient

* .get ()
* .post ()
* .put ()
* .delete ()


---

<img src="/media/angular-images/angular-12/get.png" alt="get n stuff">


---

<img src="/media/angular-images/angular-12/put.png" alt="put n stuff">


---

### Check Examples on Github!</h3>