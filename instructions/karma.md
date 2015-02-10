# Karma Instructions

## Table of Contents

1. [Introduction](#introduction)
1. [Karma](#karma)
1. [Karma-chai](#karma-chai)
1. [Karma-mocha](#karma-mocha)
1. [karma-phantomjs-launcher](#phantomjs)
1. [Karma-sinon-chai](#karma-sinon-chai)
1. [Karma-mocha-reporter](#karma-mocha-reporter)
1. [Example karma config](#default-configuration)

## Introduction

This guideline describes how to set up a testing stack for front-end projects.
It's hard to define a (check)list of libraries, that should be used at every project.
This is because libraries are changing every day.
Besides that, projects are not always the same, so sometimes a different set of tools fit the project better.
The tooling below is based on a project we worked on recently.

## Karma

>Karma is essentially a tool which spawns a web server that executes source
>code against test code for each of the browsers connected. The results for
>each test against each browser are examined and displayed via the command
>line to the developer such that they can see which browsers and tests passed
>or failed.

* [Karma](http://karma-runner.github.io/)
* [Karma on GitHub](https://github.com/karma-runner/karma)

You can start quickly with [Karma](http://karma-runner.github.io/) by running (after the installation) `$ karma init `.
Below are some useful defaults when developing a project in [digitpaint/roger](https://github.com/DigitPaint/roger).
```
    files: [
      'test/test-main.js',
      {pattern: 'html/javascripts/src/**/*.js', included: false},
      {pattern: 'html/vendor/**/*.js', included: false},
      {pattern: 'test/**/*.js', included: false}
    ],

    // list of files to exclude
    exclude: [
        'html/vendor/rjs/**/*.js'
    ],
```

## Karma-Chai

>Chai is a BDD / TDD assertion library for node and the browser that can be
>delightfully paired with any javascript testing framework.

* [Chai Assertion Library](http://chaijs.com/)
* [Karma-chai](https://github.com/xdissent/karma-chai)

Chai has several interfaces that allow the developer to choose the most comfortable.
Our standard is to use the chain-capable BDD style so the test is in an expressive language & readable style.
For example:
```
    // Bad
    chai.should();

    foo.should.be.a('string');
    foo.should.equal('bar');
    tea.should.have.property('flavors').with.length(3);

    // Bad
    var assert = chai.assert;

    assert.typeOf(foo, 'string');
    assert.equal(foo, 'bar');
    assert.property(tea, 'flavors');
    assert.lengthOf(tea.flavors, 3);

    // Good
    var expect = chai.expect;

    expect(foo).to.be.a('string');
    expect(foo).to.equal('bar');
    expect(tea).to.have.property('flavors').with.length(3);
```

## Karma-Mocha

>Mocha is a feature-rich JavaScript test framework running on node.js and the
>browser, making asynchronous testing simple and fun. Mocha tests run
>serially, allowing for flexible and accurate reporting, while mapping
>uncaught exceptions to the correct test cases.

* [Mocha](http://mochajs.org/#installation)
* [Karma-mocha](https://github.com/karma-runner/karma-mocha)

From the mocha "interface" system, our standard is to use BDD style.
The BDD style provides us functions like: describe(), it(), before(), after(), beforeEach() and afterEach().

So an example test could look like:
```
    beforeEach(function(){
        this.model = new Facet();
    });


    afterEach(function(){
      this.model = null;
    });

    describe("by default", function(){
      it("should have an empty .facetItems collection", function(){
        expect(this.model.facetItems).to.not.be.undefined;
        expect(this.model.facetItems.length).to.equal(0);
      });
    });
```

This should be configured in the karma.conf.js file, with the following block:
```
    client: {
      mocha: {
        ui: 'bdd'
      }
    },
```

## PhantomJS

>PhantomJS is a headless WebKit scriptable with a JavaScript API. It has fast
>and native support for various web standards: DOM handling, CSS selector,
>JSON, Canvas, and SVG.

* PhantomJS: [PhantomJS](http://phantomjs.org/)
* [Karma-phantomjs-launcher](https://github.com/karma-runner/karma-phantomjs-launcher)

We use PhantomJS as headless browser launcher, to run our tests against.
Headless means no window will pop-up when running the tests.
It's not necessary to use a browser launcher.
But it has it advantage that when starting karma server, we've already one browser to test against (PhantomJS).

Configure it in the karma.conf.js as follows:
```
    browsers: ['PhantomJS'],
```

## Karma-sinon-chai

>Standalone test spies, stubs and mocks for JavaScript.
>No dependencies, works with any unit testing framework.

* [Sinonjs](http://sinonjs.org/)
* [karma-sinon-chai](https://github.com/kmees/karma-sinon-chai)

We use Sinon to create spies, stubs and mocks.
E.g. when we want to test a callbacks; we can test that a callback is called once or multiple times when running our code.
This can be very useful when working with asynchronous HTTP requests or events, with spies we can verify the behaviour of our code.

Example:
```
    it("should propagate the 'change:selection' event from children", function(){
      this.collection.set([{type: "a"}, {type: "b"}]);
      var callback = sinon.spy();

      this.collection.on("change:selection", callback);
      this.collection.get("a").trigger("change:selection");

      expect(callback).to.be.calledOnce;
    });
```

Configure it in the karma.conf.js as follows:
```
    frameworks: ['mocha', 'requirejs', 'sinon-chai'],
```

## Karma-mocha-reporter

To give our logging of the test some body, our standard is to use karma-mocha-reporter instead of dots or progress reporters.

This gives us for example to following logging when running the tests:
```
    A Facet
        by default
            ✔ should have an empty .facetItems collection
```

Configure it in the karma.conf.js file as follow:
```
    reporters: ['mocha'],
```

## Default configuration

After adding the tools described above, your default karma.conf.js would look something like:
```
    // Karma configuration
    // Generated on Mon Aug 11 2014 12:19:16 GMT+0200 (CEST)

    module.exports = function(config) {
      config.set({

        // base path that will be used to resolve all patterns (eg. files, exclude)
        basePath: '',

        // frameworks to use
        // available frameworks: https://npmjs.org/browse/keyword/karma-adapter
        frameworks: ['mocha', 'requirejs', 'sinon-chai'],


        // list of files / patterns to load in the browser
        files: [
          'test/test-main.js',
          {pattern: 'html/javascripts/src/**/*.js', included: false},
          {pattern: 'html/vendor/**/*.js', included: false},
          {pattern: 'test/**/*.js', included: false}
        ],

        client: {
          mocha: {
            ui: 'bdd'
          }
        },


        // list of files to exclude
        exclude: [
            'html/vendor/rjs/**/*.js'
        ],

        // preprocess matching files before serving them to the browser
        // available preprocessors: https://npmjs.org/browse/keyword/karma-preprocessor
        preprocessors: {
        },


        // test results reporter to use
        // possible values: 'dots', 'progress'
        // available reporters: https://npmjs.org/browse/keyword/karma-reporter
        reporters: ['mocha'],


        // web server port
        port: 9876,


        // enable / disable colors in the output (reporters and logs)
        colors: true,


        // level of logging
        // possible values: config.LOG_DISABLE || config.LOG_ERROR || config.LOG_WARN || config.LOG_INFO || config.LOG_DEBUG
        logLevel: config.LOG_INFO,


        // enable / disable watching file and executing tests whenever any file changes
        autoWatch: true,


        // start these browsers
        // available browser launchers: https://npmjs.org/browse/keyword/karma-launcher
        browsers: ['PhantomJS'],


        // Continuous Integration mode
        // if true, Karma captures browsers, runs the tests and exits
        singleRun: false
      });
    };
```