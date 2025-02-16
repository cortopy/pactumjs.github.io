# Reporting

Tests we run through pactum can also generate reports. By default, pactum comes with no reporters. We need to manually add & enable required reporters.

## Adding a Reporter

Reporters are available as individual packages. Install the required reporter as your dependency.

```shell
npm install --save-dev pactum-json-reporter
```

We can add *n* number of reporters to the pactum tests. Use `pactum.reporter.add` method to add reporters at the beginning of your execution. Run `pactum.reporter.end` method at the end of your test execution.

!> As pactum is not tightly coupled with any of the test runners, it is required to run the `reporter.end()` function at the end of your test execution to let pactum generate the reports.

```js
const pjr = require('pactum-json-reporter');
const pactum = require('pactum');
const reporter = pactum.reporter;

// global before block
before(() => {
  reporter.add(pjr);
});

// global after block
after(() => {
  return reporter.end();
});
```

### Reporting for BDD

The reporting structure for [Breaking](api-testing?id=testing-style) testing style differs as the `spec` runs in multiple steps. To have a proper reporting, we need to run additional methods.

- Run `settings.setReporterAutoRun(false)` before test execution.
- Run `spec.end()` after each test case.
- Use `spec.response()` for assertions.

<!-- tabs:start -->

### ** base.spec.js **

```js
const { settings } = require('pactum');

// global hook
before(() => {
  settings.setReporterAutoRun(false);
});
```

### ** user.spec.js **

```js
const pactum = require('pactum');

describe('should have a user with name bolt', () => {

  before(() => {
    this.spec = pactum.spec();
  });

  it('given a user is requested', () => {
    this.spec.get('http://localhost:9393/api/users');
  });

  it('should return a response', async () => {
    await this.spec.toss();
  });

  it('should return a status 200', () => {
    this.spec.response().to.have.status(200);
  });

  after(() => {
    this.spec.end();
  });

});
```

<!-- tabs:end -->



## Available Reporters

* [pactum-json-reporter](https://www.npmjs.com/package/pactum-json-reporter)
* [pactum-swagger-coverage](https://www.npmjs.com/package/pactum-swagger-coverage)
* [pactum-influxdb-reporter](https://www.npmjs.com/package/pactum-influxdb-reporter)
* [pactum-flow-plugin](https://www.npmjs.com/package/pactum-flow-plugin)

?> More reporters are on the way

## Write a Custom Reporter

A reporter should have the following methods in it.

* `afterSpec` - will be called after each spec
* `afterStep` - will be called after each step in e2e testing
* `afterTest` - will be called after each test in e2e testing
* `end` - will be called at the end. It can return a promise.

See the above reporters source code for usage examples.