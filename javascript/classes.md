# Classes

## When to use a class?

Reference: [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using\_classes#why\_classes](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_classes#why_classes)

A class can be used when the object needs to store a set of data and possibly contain actions to be called on the data sets (think of objects such as `Map` or `Set` from javascript) or when a set of behaviours should be exposed at once (think of objects like the `Date` object in javascript or a class for playing, pausing and rewinding a video).

If a class only contains one behaviour that is exposed and no real data storage system, it should be a normal function outside of the class structure.

 **Wrong**

This is a class that does have some level of behaviour exposure but only one, in this case a single function would have been a better use case.

```javascript
class Title {
  constructor(element) {
    this.element = element;
  }

  set(text) {
    this.element.innerHTML = text;
  }
}

```

  **Correct**

This is a useful case for a class (don't mind the inner workings of the functions), this class exposes the object to a set of new functions that are all centered around one thing (manipulating the video).

```javascript
class Video {
  constructor(title, video) {
    this.title = title;
    this.video = video;
  }

  play() {
    console.log(this.title + " is playing");
    video.start(video.currentPosition);
  }

  pause() {
    console.log(this.title + " is paused");
    video.pause();
  }

  stop() {
    console.log(this.title + " is stopped");
    video.stop();
  }

  rewind() {
    console.log(this.title + " is rewinding");
    video.rewind(0);
  }
}

```

## Order by visibility

There is a certain hierarchy withing a class that should be followed at all times, first the properties of the class should be declared, after which comes the constructor, then the accessor fields and lastly the seperate methods.

The properties and methods should both be ordered by *public* items first and be followed by the *private* items.

## Accessor fields

Reference: [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using\_classes#accessor\_fields](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_classes#accessor_fields)

Within a class we usually use a lot of arrays and objects. Within classes we want to use the accessor fields / functions to access and set fields within these arrays. Here's an example of how this could be implemented.

```javascript
// File: color.js
class Color {
  #values;
  constructor(r, g, b) {
    this.#values = [r, g, b];
  }

  get red() {
    return this.#values[0];
  }

  get green() {
    return this.#values[1];
  }

  get blue() {
    return this.#values[2];
  }

  set red(value) {
    this.#values[0] = value;
  }

  set green(value) {
    this.#values[1] = value;
  }

  set blue(value) {
    this.#values[2] = value;
  }
}

// File: main.js
const colorClass = new Color(255, 0, 0);
colorClass.red = 10;

document.getElementById("response").innerHTML = colorClass.red;

```

