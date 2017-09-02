`return Promise.resolve().then(function() { ...original code... }).then(...)`

Je kan een functie die als laatste argument een `callback(err, response)` heeft ook wrappen met `Promise.promisify()` (of promisifyAll() voor volledige requires te promisen)
Een sync method wrappen kan met `Promise.method`. Je hebt wat sync code voordat je async begint: `Promise.try`.


Configuratie:
Zet tijdens development, testing, ... de warnings op met:
var Promise = require('bluebird');
Promise.config({
  longStackTraces: true,
  warnings: true
});
(ik heb dit vb toegevoegd in de spec/ folder zodat Jasmine alle warnings toont)


bluebird gotchas:
- Promise.all() reject van zodra er 1 van de promises reject
- Geen return in een then: return altijd een promise of een value (geen return is ook een bluebird warning)
- Gebruik Promise.join voor fixed size joins. Gebruik Promise.all voor dynamische collecties
- Promise.map, Promise.each, Promise.filter, Promise.some, ... bestaan ook allemaal!


manual chaining:
var p = Promise.resolve();
if (something) {
  p = p.then(...);
}
if (somethingElse) {
  p = p.then(...);
}
return p.then(...);


bluebird shortcuts:
DO NOT:
.then(function() {
  return new Promise(function(resolve){
    resolve(result.children[0]);
  });
});
DO:
.then(function() {
  return result.children[0];
});


DO NOT:
.then(function() {
  return new Promise(function(resolve, reject){
    reject();
  });
});
DO:
.then(function() {
  return Promise.reject();
});


Returning multiple values:
.then(function() {
  return ['someValue', Promise.resolve('whee')];
})
.spread(someValue, whee) {
  console.log('SPREAD:', someValue, whee);
});


Working with state:
Promise.resolve().bind({})
.then(function() {
  this.foo = 'bar';
  return null;
})
.then() {
  console.log(this.foo);
});

Extra logging toevoegen:
.tap(console.log.bind(console))


Errors:
Alles dat reject() wordt dat geen Error object is, resulteert in een bluebird warning.

Correcte syntax:
throw new Error('....');
reject(new Error('...'));
Promise.reject(new Error('...'));

Zelf een custom error maken:
module.exports = function(msg, statusCode) {
  var err = Error.call(this, msg);
  err.name = 'IslpSearchError';
  err.statusCode = 500 || statusCode;
  return err;
};