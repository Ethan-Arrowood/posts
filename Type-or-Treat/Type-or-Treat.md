---
title: TypeScript - Type | Treat Halloween Challenge
published: false
description: 
tags: 
---

# Type | Treat Challenge

This halloween, the TypeScript team has released a series of challenges for us to solve. In this post I'll be going over all of my solutions to the challenges. 

If you have any questions about my solutions or want to share your own, comment below!

> The day I was planning on publishing this article, I realized the TypeScript team posted their own solutions for each of the challenges. If taken a bit extra time to expand upon my answers and try to provide additional information about the TypeScript patterns used in the solutions.

## Challenge 1

Try it for yourself [here](https://dev.to/typescript/type-treat-challenge-1-829)

### Beginner/Learner - Ghosts and Hauntings

The first challenge solution requires replacing the `any` argument in `displayHauntings`. The previous method `displayGhost` uses the `displayHauntings` method to print out the array of _hauntings_ defined by the `GhostAPIResponse`. Unfortunately, the _hauntings_ type is inlined with `GhostAPIResponse` interface and the challenge specifies **not** to modify `GhostAPIResponse`.

My first solution is to infer the item type of `GhostAPIResponse.hauntings` array using a custom utility type:

```ts
type ExtractArrayType<Arr> = Arr extends (infer Element)[] ? Element : never
```

To better understand this utility, lets break it down piece by piece. The type itself has a single generic `Arr`. The TypeScript interpreter attempts to match the generic with the type `(infer Element)[]`. This type uses the `infer` keyword to specify a second generic, `Element`. The interpreter is smart enough to assign whatever the item type of `Arr` is to the generic `Element`, and then since the interpreter has successfully matched the generic `Arr` to the type `(infer Element)[]` it returns the inferred element type `Element`. And if the interpreter is not able to match `Arr`, the utility returns `never`; preventing misuse.

Finally, to complete the challenge I replaced `any` with `ExtractArrayType<GhostAPIResponse['hauntings']>`, and discovered that there was in fact a type error on line 41 (`provemance` -> `provenance`).

Check out my challenge solution [playground](https://www.typescriptlang.org/play?#code/PTAEE0HsFcHICcCmoC2BDA1gSwHYHNQ0dCAHE0AdwAssBjK0WyHHRWgFwGdR3JDQAJmnZoARmk6IAUCFCQAZqAA2kWmiWg8VSJy6EcAnlURZ4oeWgBuMeFnbIqaaDi4A6KTLBRohJIyTCuATsxoxKWIgulDT0jMysHNy8RsKCwmISiAA0nvqGAJ4woI6WyESgAIIACgCSoEicJMySmpGI8MKIhvKQZoU+Trzo7HTqSvnuuQDqxsT9oLpo8Ow88PlBPHwCWI1KaPlGDk4uQZxZoPMB4QBe0rIhqfMCzLArGDiQFMWfmzz5JMgQsgSEs0ChEPYzERDAAraC6UDw5AAAyI+WRk1kqJw6OKElAdm4JD2tGyoFE0BWaDiSiUiDQeGgyCQTBQ4IMXRSK3mahwuSRoFgNQ5AA9OQAVf6ITiwX5IFCQUqHQVo2UUOzaSmMRz4DZA3IAcW0umqNQAStKmjgWuwpZNbQDQEadOxTRbGs1kABeUAAbykoFAODBiAAXAt2LZ8AHyaYQgARToAfnDuijeBjAnpCeTqcjQRjoiwkDz6Y8gcczhG+E44Yq8A6+QAPL6eHY6aWgucSPBFZEiKTO-hzio1CNmEOCABfAB8UinHiY1pW212+2dCJ9AAotC7wxvXbV3VbJABKUBemd+mNLziQOmuFR4LfIgByIfDABJfbvdK5g+CU7IqeMZYIoO7GuwrhFssVCJvY563veiCPpAz7IgAQnGVBCPkX4-pB0HYfBiBASBgZgaAEEuq4WbCHBnSIc0yGoeh8YRAI+G-lBdE5vYZE3sxD5Pi+AA6OBYSWoDftxRGQAJgnWixInIuJ9bsBQvQYLWwExrJlYnDWrg9PAACiaD0FuSwaVpF5Xv6gaBquxL7AAEsc1Z4JwVnLJp8AYORoBTiBC5SA6yCmSKkYWa6Db7JKAJNvW8BXj6yWgIgUWRAI3Bbrg8jtKApl0uy7CngA2gAuqASZFSVkQrOGrClPAHiyN4Zh4JA6jhvKfZGDs+j5Oc6ohDAKz0EQeAbAebqWp6Ui3iuOwufk7lVqcF5UQZnnhpF0UcMl8VSk2s1HvN1qIOVsA7acsCVTO56XtegZIcJaEvqAAC0oDiu2YbSb6t34K4IzsHSAmvUJKEqY537A3gqFjsWOCQ3ESnvehcNAx5QSuD2fbBjgpICaFQA)

Looking back on my first solution, I wouldn't consider the `infer` keyword something a _beginner/learner_ would know. So, there had to be another solution. This got me thinking more about the `Array` type.

The `Array` interface is defined here in [lib.es5.d.ts](https://github.com/microsoft/TypeScript/blob/71a2c59c557d871a1d38d10df83cfc36dc10d887/lib/lib.es5.d.ts#L1220). It has one generic `T`, and the definition has an [index type](https://www.typescriptlang.org/docs/handbook/advanced-types.html#index-types) of `[n: number]: T`. 

Using the index type definition, the solution can be simplified to: `GhostAPIResponse['hauntings'][number]`. No inference necessary!

Here is the updated solution [playground](https://www.typescriptlang.org/play?#code/PTAEE0HsFcHICcCmoC2BDA1gSwHYHNQ0dCAHE0AdwAssBjK0WyHHRWgFwGdR3JDQAJmnZoARmk6IAUCFCQAZqAA2kWmiWg8VSJy6EcAnlURZ4oeWgBuMeFnbIqaaDi4A6KTLBRohJIyTCuATsxoxKWIgulDT0jMysHNy8RsKCwmISiAA0nvqGAJ4woI6WyESgAIIACgCSoEicJMySmpGI8MKIhvKQZoU+Trzo7HTqSvnuuQDqxsT9oLpo8Ow88PlBPHwCWI1KaPlGDk4uQZxZoPMB4QBe0rIhqfMCzLArGDiQFMWfmzz5JMgQsgSEs0ChEPYzERDAAraC6UDw5AAAyI+WRk1kqJw6OKElAdm4JD2tGyoFE0BWaDiSiUiDQeGgyCQTBQ4IMXRSK3mahwuSRoFgNQ5AA9OQAVf6ITiwX5IFCQUqHQVo2UUOzaSmMRz4DZA3IAcW0umqNQAStKmjgWuwpZNbQDQEadOxTRbGs1kABeUAAbykoFAODBiAAXAt2LZ8AHyaYQgARToAfnDuijeBjAnpCeTqcjQRjoiwkDz6Y8gcczhG+E44Yq8A6+QAPL6eHY6aWgucSPBFZEiKTO-hzio1CNmEOCABfAB8UinHiY1pW212+2dCJ9AAotC7wxvXbV3VbJABKUBemd+mNLziQOmuFR4LfIgByIfDABJfbvdK5g+CU7IqeMZYIoO7GuwrhFssVCJvY563veiCPpAz7IgAQnGVBCPkX4-pB0HYfBiBASBgZgaAEEuq4WbCHBnSIc0yGoeh8YRAI+G-lBdE5vYZE3sxD5Pi+AA6OBYSWoDftxRGQAJgnWixInIuJ9bsBQvQYLWwExrJlYnDWrg9PAACiaD0FuSwaVpF5Xv6gaBquxL7AAEsc1Z4JwVnLJp8AYORoBTiBC65N4Zh4JA6jhvKfZGDs+j5Oc6ohDAKz0EQeAbAebqWp6Ui3iuOwufk7lVqcF5UQZnn7pBuUetaiAANqwNVpywAAuk1ODQCgojtB156XtegZIcJaEvqAAC0oDiu2YbSb6bX4K4IzsHSAmjUJKEqY537LXgqFjsWOCbXESnjehe1LR5QSuD2fbBjgpICaFQA).

### Intermediate/Advanced - Trick or Treat

This challenge was a blast for me. One of my favorite math subjects is set theory. 

The challenge starts with a union of halloween tricks or treats, `ResultsFromHalloween`. Each item in the union has either a `candy: true` property or a `trick: true` property. The first step in the challenge is to sort the union between tricks and treats.

Luckily, there is a built-in utility type.

> `Extract<Type, Union>` constructs a type by extracting from `Type` all union members that are assignable to `Union`.

```ts
type AllCandies = Extract<ResultsFromHalloween, {candy: true}>
type AllTricks = Extract<ResultsFromHalloween, {trick: true}>
``` 

Under the hood, the `Extract` type utilizes conditional types:

```ts
type Extract<T, U> = T extends U ? T : never
```

For each discrete item in `T`, the interpreter checks if it satisfies the conditional `T extends U`, if it does then that item is added to the resulting union.

The challenge has one more task, create another type that _excludes_ candies with peanuts in them. But, the treat types only describe if they have peanuts in them, not if they don't. Now the solution requires the inverse of extracting, _excluding_. 

> More set theory! Yay!

```ts
type AllCandiesWithoutPeanuts = Exclude<AllCandies, {peanuts: true}>
```

Yet again, TypeScript has a utility type for this operation. 

> `Exclude<Type, ExcludedUnion>` constructs a type by excluding from `Type` all union members that are assignable to `ExcludedUnion`.

This one also uses conditional types:

```ts
type Exclude<T, EU> = T extends EU ? never : T
```

For each discrete item in `T`, the interpreter checks if it satisfies the conditional `T extends U`, if it **doesn't** only then is that item added to the resulting union.

Check out my challenge solution [playground](https://www.typescriptlang.org/play?#code/PTAEE0HsFcCdQNYEsAmBnUALAhgNwKaiz4AucAdvilpALaEDuSJmo2oDmkANoQEbYA5gCgQoAGbRu3UJHFZs0yA3z5yobpEgkANG3LUAnjADkBUCWwI1FzIRJJ6FyKFFhaVwuxT40AY1gkAAcHSHU5NmlZeRZ8WgAuYWESQyDCAGVybD8EACFsWAwAXlAAb1dQUCz6eNAAInSuWBJQAGEuPx5sEkJ8wrrhSuxaGHISWoAWQdA-bANDWpJYaHxhAF8klLTQAHFoWgFpYrKKquH8WrqdrXxDXf3D7jQBys7NWEvBYjU60AAfepBOBBXjPaazeaLZarDbJVKEACCQRBvlAJXK02qF3qSJRYNecxQCws0PWm3hbS0yFR6NOWMurSpSF8LxmhOJSxW0zSc2gJDQUK5sK2GXISBy+EKfTRJ0x50umXF1kKoD6rIhRMFq0qPPIfIFJKF5O2ABUtCwgtg0D0ZRjKvT6mbtJhLdb8KzaEgxhzSZUlkqtWS4dsAApqPxIGS0uU1eph8gR7is-05QOwsRQaAzYjdLygNBewS8UBBSOECKKGSxIi+KT8vSEjhzFokFzQNCrMQsJAYVv5nDEWR82yML1i8iCDDDMKCUDGOCIVBoAB0rmDhAASrXuPyAGKwOgACUrylU6hKmWyeQKGABewOldvoFxoP+oEVEqlBTfjMg1KfToWlaNoAvGiZJBmpiDuISCFC0giQIWzhVKo1B9h2lqwLmQ4tnYbLzBIB60COJL4N0aA6G4c4wPhoAoC4LDdKAuBIOwnQGMwSBhIoFjwiuxqItIrSEsyxwAKIAB5LNkJAADxbmgdZoPuR4nioah6KUGo+isawAHyiAAVIZgyGbx2ySdJfhySaegAKp6TKJqgPgUlqOgoB2aAAD8oDObUlAELApnAOuz7SCagQ5OJUlYdZ8nbnuRHHko6nkJpKYIFq+kQWACLcCM1ojsQeh8MO3DMCQxYmLkkB8HwdwmtgfCgiY+HkNoCjmLq+qrsJ5BUfOoAeNYbAaD2LQRBqomgAAVu2LTiJA8CYI43kCeF3D9SgokAOrMFwfJhry-IypJfjcNAPiyflW0ib4mk9fy2UGcAxmmeZhDnZd122aAYkOU5LluQYGAAz5KFBaAtQmiFwhAA)

## Challenge 2

Try it for yourself [here](https://dev.to/typescript/type-treat-challenge-2-3n16)

### Beginner/Learner - Pumpkin Patch

This challenge tasks us to make use of the `typeof` operator. TypeScript is quite smart as long as you provide it with the right information. For the first part of this challenge the solution is to derive the `Pumpkin` type from the `pumpkin` object already defined using `typeof pumpkin`. The second part of the challenge uses a TypeScript utility type `ReturnType<F>`. The solution here is to pass the derived type of the function to the `ReturnType` utility: `ReturnType<typeof createExamplePumpkin>`.

To better understand how `ReturnType` works take a look at the [implementation](https://github.com/microsoft/TypeScript/blob/71a2c59c557d871a1d38d10df83cfc36dc10d887/lib/lib.es5.d.ts#L1524)

```ts
type ReturnType<T extends (...args: any) => any> = T extends (...args: any) => infer R ? R : any;
```

The type specifies a generic `T` that is constrained to a function with `any` arguments and `any` return value (i.e. `(...args: any) => any`). This generic `T` is then matched against the type `(...args: any) => infer R`. Here, the interpreter infers the return value of the function and assigns it the parameter `R`. If this match is successful, then the type `R` is returned.

Check out my challenge solution [playground](https://www.typescriptlang.org/play?#code/PTAEE0HsFdQCwIYDcCmoGlQJwJ6gM4AOKAxgJYIA2oh0AtoQNZkB2ANOiwCag4ygB3BCwAuoEZFAksKBCLQYROYqABmWSHVBkRAOgBQ+kKAAqWBFx1lILKpTxFZzFgHMOfWCRiUeXFKtYFcWU0SCxtURQsVQQSFCMwVTDxODR8RGIOACNoMQ90GVAbe1AXSFYXcUlEVBSyfCKWNFoGZzYEgkl8rhsAcjFVFBRqSjJGNB1ehoEwkThQLmhCUZI5CpS0PwCWKxsDfS8WfDEWplZQAF5QAG99UClISjCALlAAIhxhp4F6uDf2+5KYivN5laBYLj-O4EMgALxQIIAsihLPQofcUE0sC4cCCAIwABgAnKAANIAKxoUVAhIJLnRam+URRrxilHwKABMPhAGFESCAKwE3QADlAAA9QAAWYUANgloAAzMKAExSOhQgC+AG5DMYAJJHeQWIqqKQyNaudDBFSjcbPBIdIFoAAK9DOLEuN1AHXuXieWFexywFV9oF0EY6msdxh5wl4-Hy0A5NpQkDNzoaEnNsnk1vwFUoaFGWIA-IZnaA3a1zldnemaO7nHqwAA5FDisRLDgyDkILAkeZwSACBOeePJoK5MijJSpqrwYRcItO1IEBB0ZoaLJFrQ-ObW1TQFgkETWFi6UAACRHUnj+Q7InMp42HRkInBnsr6k0dQaR5PM8bAXFwUDEOY0g3NBnXLA4bGOHM5BQABRcUN2WFBqw9L0AAoAEpLgAPhuaF30-RsaxYfRo30SssOcAAxDQ6AY49T3PL0ACUwM-EwQgAHnrM1pFzFC0IYIt6NYQj9CAA)

### Intermediate/Advanced - Ghosts, Gods, and Demons oh my!

> Stay on your guard.

Thank you challenge author, hint received! The solution for this challenge makes use of [type guards](https://www.typescriptlang.org/docs/handbook/advanced-types.html#user-defined-type-guards). The challenge tasks us to create three user-defined type guards `areGods`, `areDemons`, and `areEctoPlasmic`.

My initial thought was to define three new types and the three functions:

```ts
type Gods = unknown
function areGods(ghosts: Ghosts[]): ghosts is Gods[] {}

type Demons = unknown
function areDemons(ghosts: Ghosts[]): ghosts is Demons[] {}

type EctoPlasmic = unknown
function areEctoPlasmic(ghosts: Ghosts[]): ghosts is EctoPlasmic[] {}
```

User-defined type guards can be unsafe if not used correctly. Whenever I create type guards, I try to think in _absolutes_. For example, the `areGods` guard must only return `true` if **all** of the `ghosts` are _gods_. This is true for other type guards as well. Since the `ghosts` argument is an array, we can use an ES6 method `Array.prototype.every` to validate that _every_ item in the array satisfies a condition. In this case, the condition is the existance and truthiness of the `god`, `demon`, or `ectoplasmic` properties. Additionally, similar to the previous challenge, the three types can be defined using `Extract` and the `Ghosts` type union.

```ts
type Gods = Extract<Ghosts, { god: true }>
function areGods(ghosts: Ghosts[]): ghosts is Gods[] {
	return ghosts.every(ghost => "god" in ghost && ghost.god)
}

type Demons = Extract<Ghosts, { demons: true }>
function areDemons(ghosts: Ghosts[]): ghosts is Demons[] {
	return ghosts.every(ghost => "demon" in ghost && ghost.demon)
}

type EctoPlasmic = Extract<Ghosts, { ectoplasmic: true }>
function areEctoPlasmic(ghosts: Ghosts[]): ghosts is EctoPlasmic[] {
	return ghosts.every(ghost => "ectoplasmic" in ghost && ghost.ectoplasmic)
}
```

In my solution, not only do I check for the existance of the relative property in the item, but also if that property is truthy. Since this challenge only ever uses `true` the truthy check is unnecessary.

Check out my challenge solution [playground](https://www.typescriptlang.org/play?#code/PTAEEkBcHIGdQEYFMkDtQEcCuBLSTQBDdJANzR1QHMAaUWAewFskHUCAbJSUBrHwhw4BYAFAhQkABYEqUhrEjwAnn2jlEKdAGNCkbVMpVQAMwYAnUKqyWTOc4qtJClyqDESEWWJSSxYAHSgACpSenCgqAw8ZGiSMqAA7haOCHjxfHJ0HmDpkDao8Hw8VAwAJvDEZaBlSExsleYEgoygTYLu4mBVRE2RDIlEQrzklgCyxGGQkMQBYjkhCQjmhJRFJlZ8lviETKBh8NrMBFgADkl4UkTogqXml0wLZpa1gkYX0vEEp+YMCFxMIKhJDKIj+JDmHjSHCVDh3B6dCQwr68VAcUGJQigtyJQwGTZYUC6dCvDgfK7SAgw2BYJBzUQLYGgsIaWDcUBnIigDgwngMDZNU4WfDVOQKJTAWr1QrAUoVBY9FnNWAAayQ1RwG2sROIphwwzcjBYvA2lJq3FWHFgCqhS2ikGYiH4-R4BmIVAIlPsRPKdPmXVAAGUZqC2ATLFQsC4yvSxJBlKcCAA1HClUAAXlAAG8xKBIrskAAuUAAIhTaaTYYAEswvOZjAARJD8WAGNBlCwmEs0XOICyoYslsYMDgd0iEEu9qRYJjEcCwKTFyDmWliAC+-vjidAAC0sFgyZmc6I86gC4O9wfu72pWwlyukL22agygAhQjaFXBBhVpBCAAUACUxakAwOBlOum4JsmlAAF4ZtmvZniwg4pqg8EAMIcMK8jXie5rSveq74c+b4fl+P5-hwQEgWBEGiBuDKiFuBAAOI4LBEIIcep7nqWaGYdhkK4T2+FykRj74YYABW5HBliAAKWAmCYEyoDRoCgeBkFMSxQY8iwliZoh+HIUWpaBgZEJ4XmRxCYOrFNGgAC0oJsp6Ui-Fgcg2aASDaA6pwcIQsBMDg2gSTpcbQaArHyIo8CZuWDCgAAPru+5kulAlpbFHFcellk4IZ-q1NowV9CYWCoAFOBhguDCfgAgi+wQrKc-5iglxZNeYKzKAAPFmfkBQwQUhWFEWSA+a4AHzAZpdH+sAABUK2gAAArUPz+Xo6qgM58TIsiCCENUZ2kOFBArcAYhlRVBBVTVkB1Tovz+MCwbtEwsAaVp9FiE9tVhpQ5CKKme0AEpIEKkKdfFSjFnF4qwAA2gAuoBJl5pqoD-i4SCseUv1dUogFYzxeagBIACqhQ2FSGzIma9xyLa7wOjUDC9nmnjOukRwHtUyTmCqPNEu9sCfcuzg-UB4tNPk5ioL2jG9hIbXhSqoJeDwRrcIY1AQEdqAqkkBDEp0vNgGEL5cKiXK6GyzkIMozlOwQp0+Na+G4-jTRNtKJMI7A5PY1Tzx40chQ8Le6D8qApOh+HVMEWwASke+n7fr+AGAeLjF5mr+ESAA6kg6gEB27BHZU3LRP0iSidb9DyELqDQDwyCSAwKVlJqdjaAekC9n7BMAKKjQpwWheF8Mo2HlN5g1zWte188Jfn+GMcXEgAPIANJ0NC8DMwk7Ymj6tRJHiVzEp3RK2x6QQLBhuraok9z4KY1XA4UmjYUGLicKVxEj6jJFwW0zQEAMA0AsI418jhMFOPqJAAB+CAMB4DYQYCqd4O1phuQhHVbwvBnSUiaC-JiEgiblG5FgT8QR5KhnQNYCMUZzAxigtuImFQELjwAB7Lg-JAAayMEp0GGuJaatJQBzUBr-F6YYCa8ODijJGIcMYLSTqAZEqiMbh0VgUROIcAixHMMoDePB0yzVLHKEsuj0Ck1AAAMhcSY8UAQ5Rb2LnpQODR+FCJWAFMRIdJFpwHDIgg8jRBAyUTcAOdQGhWNgBolGWjiw6ORP4woBjKZGOVh4hKZjRiWOcTY0sccHFuGcW4opkAAhxx8dwggk8HTTwmuFQJwiQniKUOE-ygUZ6TQknI2aCjnqvV6EgNpDAOmz20CktJCUMn1NPvAWZ8zJp5N7AUpxpjzFlIRhmWxJZBljWGeFap+zxSuPcaTMxo1xoLJ8UAA)

## Challenge 3

Try it for yourself [here](https://dev.to/typescript/type-treat-challenge-3-446j)

### Beginner/Learner - House Types

Starting off, the challenge tasks us to define three types that correspond to the three arrays:

```ts
type TreatHouse = {
  location: string,
  result: 'treat',
  treat: {
    candy: string,
    baked?: string,
  },
}

type TrickHouse = {
  location: string,
  result: 'trick',
  trick: string,
}

type NoShowHouse = {
  location: string,
  result: 'no-show',
}
```

The challenge then leads us to simplifying these types. Two properties `location` and `result` exist in each type, and the `result` type is only three literal values. So extrapolate that first:

```ts
type House = {
  location: string,
  result: 'treat' | 'trick' | 'no-show',
}
```

Next, intersect the `House` type with the previous three types and remove the `location` property. I kept the `result` property specified to the corresponding literal.

```ts
type TreatHouse = House & {
  result: 'treat',
  treat: {
    candy: string,
    baked?: string,
  },
}

type TrickHouse = House & {
  result: 'trick',
  trick: string,
}

type NoShowHouse = House & {
  result: 'no-show'
}
```

In a real-code examples this pattern is very useful. If you're interested check out how I used it in [Fastify's type definitions](https://github.com/fastify/fastify/blob/449fc5c25e7d2e277e68bb3f4416cfd858a71cec/fastify.d.ts#L50-L78) for the different http/s/2 server options objects.

Check out my challenge solution [playground](https://www.typescriptlang.org/play?#code/PTAEE0HsFcHIDcCmoBGjEDtQGt0AcBLDAc1AENQATAsgJwE9RIAzUemW0AF1oIGNsTTj0RkuAKBChaiAM7QANl1mhmkTu2idZIxFwA03ABaJGxTIlpkFCxs2sLQRLpFCSwXIzOR8xiYuoEcgBc3DJisoY8-NgqZBiUoBiQALSyRpAA7rIAdOLuoADC8WwwoAC2ZLjG3tz0eHKgmUb8RqC+WJRyfLxoTMwFnnLIMvJKsgD8+Vz1yAAq4VwAEjCyyAC8oADe4qCgCpC+XASQGKE6vCT6u9JyilyhsCJisNd7zw-bN3sdlPTn0Su31QVUQlAmAMuxDeoAAvtdYfk+KcdGFRMpQJsANo3Lb7Q5iE5nUAAIhW0DWoAAjCTDKN7qESR9aWixKE8b9-qSADJE0AAIToJLhcJheIORyJjPJlIAzCz6UpGcyoot2e14n9GQBZOgqQW0YWw0W4-GS07S1bIAAsCruStJKtZnzxKFBlEZhUgkGwQVkLM5jIASug1rB-SLEQBdaazUALGIyjZfPYSwkW0AXIjQm6Kz5PXgCV43aICSHZhFIlFcMIxFTY01p44ZslW0AAJjtY0+TML2BZpewOrIxH4RrFZvTxNbFOQAHYuwzHX2B33GQBlLR4XhrShG8Qx8QzBqgABykHXGUyScxKcnzeJWaBezzj2SaSvxcR4mRGFRyUvLJ61AHE9nFAkH0tWdQAANkXB0SXfdIsn3Q8pHPTJjDEUpoFAIwyCQYxkGPORDA6HCKlBIjygKZIa0oaA8AUfg-FAWAmyJWByASNi8y4lBGApbM3CkChoAwPk1E4WA+O4xIKAwRBMOcSw1j4B86gaKYjzjG9Nh2VMIKlTNARzF97XzD4uIAHzYwcbLYpDP0rHSTwWdEk3bW8bwAMjvV87MWYt3jVO8fk1LknzMvYQVwcFy2fE1vxI+M+087y2z8gzbm7R57JhQcEpzZK43PQDrytLzNl8-yLLfVJkMyWBxFhIA)

### Intermediate/Advanced - Trunk or Treat

The Trunk or Treat challenge starts with an array of _spots_. It then has a type `TrunkOrTreatResults` that has a lot of repetitive property definitions. The challenge tells us that it is already out of sync with the list of spots... so rather than more copy-pasting, what can we do to fix this type?

Consider what the type actually is representing, a _map_ of the spots listed in `trunkOrTreatSpots`. And in TypeScript there is a concept called [mapped types](https://www.typescriptlang.org/docs/handbook/advanced-types.html#mapped-types). Often times, mapped types are used to transform one interface into another such as:

```ts
interface Person {
	name: string;
	age: number;
}

interface PersonPartial {
	[P in keyof Person]?: Person[P];
}
```

But the `trunkOrTreatSpots` is an array constant, how would one write a mapped type expression for a list of strings? Think back to the day 1 challenge; use `[number]` to get the index type of the list. Then, using the `typeof` keyword we can transform that index type returned from `trunkOrTreatSpots[number]` into a union of the strings. And now with this we can pass it to a mapped type expression:

```ts
type TrunkOrTreatResults = {
	[k in typeof trunkOrTreatSpots[number]]: {
		done: boolean,
		who: string,
		loot: Record<string, any>
	}
}
```

The new `TrunkOrTreatResults` type now automatically includes _any_ value that exists in `trunkOrTreatSpots`. One important detail though, `trunkOrTreatSpots` must be a constant (denoted by `as const`). Since all of this needs to be static, TypeScript needs to be certain `trunkOrTreatsSpots` won't change in order for your code to remain type safe.

Check out my challenge solution [playground](https://www.typescriptlang.org/play?#code/PTAEBEEMDsHMBpQGEDyA1AkuAjATlABaQDOoATgKaQA21AnqAA4CuALqJKAEYCW0AJqAD20UKwIVQ0SK2aVhAMzFkeAYwDWwssqqs+sAHQAoEKACaQud2pCNhEsMasAtBUGshoPsVZVB-T05WMmZoTSFtYN0vaB8-YxMwDFFOEOICMU9GSkZIeQUIsQlQCgA3CmhWRDpLezJ+VSF+NyKKRPIKYmZqVlI+D1bQAHIAFRCwlDIx3QAlTu7eobE6RkkAdwI1DJ5SGkpIfjp2y3YhJX4ZSWcvViHSAFsd4n0OUAUKNaYyIVWyPU6AITtJAwUA1ZgdNYqXzDMahdSTaYyOZdHrEJYOUFCLgAKwoqnYrBWknEMnaPkgdD6omIdGgqlAax44kGPhUcGpw2C8MR+1YAGVGEJFgB+IxGRqxQnjBFTPmC4WkAC8oAA2kZQLDigAFPLqIaIDXDAASlmIkgAxNgDaAjUNTcxzaALQAmA12pARaAUbT8gg-d2aoba6iQVSSM6gADqEXSPEY7oAuhxSJKfOKiasxKAVZmKJHuRM5boFb1VdBmPcuD7ExniaA4UWkawUQtlaAAN5GzWqzR8ZarAsy3kloVlitVmuJgBcne7moXAW9s64QiE1Co0Hg84XjP9s7Z+m3u93NmFs7mjXqAB5D3BEDA6AA+ecAXyM76MClCBJ4IlA9yQOoFAjCg4AoAAFMQY7ELOeZDjyxYyKWxAAJSzo2srNq2aJzrulCyGQNIwQYlD8Mw4YQRB2RlIgqhyJQlSoTmT54SemoEXIohdux7EGPxNGlMevG7qq9FkIxrAzmxInsUuFCzgoNDmsJsknmerCzh2r6qWpC4bEIs4AERGTuvHviJFkLjpnavimDbDkhLbzGiqEfuKpgWBCqigoJPAfIMlCor0oClDwnBGTMoRGRwq7lOKaaEk0ngqoBwGgeBEGFlh8owW5abrhQBg2LAWXJW57QxmQ6jEEYHgBKqRkjDqepGYmBjyTmyjMG07R+pY1CCFV6h1cljWekRPqgP1jBtcVa7sCq2keWAABikA8NQtX1UIjUOk6FoAMxzZ1KpKVtvV9UKm0+s4OwZD8egiDQYjxrs8icO8nzUHwnTcBQNifOIOy2uKYPgyt0Z5NA+izgqN1kLVkPYAYDabH0uwAZAjCrO4xKkNk2IbvcEPtC6qPNT6kgg5wiVkBtlTCKI4iSIBOMtHmHBkLAlYVKwpOmIdqNeaAPmiJQziOpInC5H8iiDL4qgEDDACOPVvN89yDNWsB8N62hKzQG5wJI2CZKAsAUISxQUAAHmG7DAVSGtCCTpgLizXP03Qs4EKwrCMLBIDNKUBgeMAebEKoKhOBHxLOFEMjOIbtAVJbzgus4h3QNgABsQA)

## Challenge 4

Try it for yourself [here](https://dev.to/typescript/type-treat-challenge-4-4dal)

### Beginner/Learner - Lock it down with Readonly

TypeScript really has a utility type for everything. This challenge tasks us with making the two given types `Rectory` and `Room` readonly so that their properties cannot be modified. One solution is to go over each property and add the `readonly` property, but as we learned yesterday, we can _map_ one type into another fairly easily. Earlier in my post I showed how one may transform an interface from being all required properties, to all optional ones. This challenge requires us to transform these types into readonly versions.

```ts
type ReadonlyRectory = {
	readonly [k in keyof Rectory]: Rectory[k]
}

type ReadonlyRoom = {
	readonly [k in keyof Room]: Room[k]
}
```

There is alot of similarities between these two types, maybe with the help of a generic we can create a utility:

```ts
type Readonly<T> = {
	readonly [k in keyof T]: T[k]
}
```

😉 And look at that, we just recreated the TypeScript utility `Readonly`. Check it out in [lib.es5.d.ts](https://github.com/microsoft/TypeScript/blob/71a2c59c557d871a1d38d10df83cfc36dc10d887/lib/lib.es5.d.ts#L1473-L1475).

For my final solution I wrapped the given types with `Readonly`:

```ts
type Rectory = Readonly<{
  rooms: Room[]
  noises: any[]
}>

type Room = Readonly<{
  name: string
  doors: number
  windows: number
  ghost?: any
}>
```

Check out my challenge solution [playground](https://www.typescriptlang.org/play?#code/PTAEDkHsFpIOwKagNZ0gdwM6nQCwIYAuoAltgOaQlzmjwA0oARgK7ECekLohu1yoTiwBOOSMOQAHADb4AxkgBQIUtgIs4hBABNm7UPlCTI0rcPIIyhAHSgAksWTVthzCznJM15WB+gAmlyikEwAVghyhNjICAiSoBaEhNS0cgQ0OswIfHC6vEiYCoiY9H6YhJDxvGSgAGbCkAC2oASSkghwKXqgjfhONIJBdGERUaDCCPja8NLsfhWg5ZU8fNiYxpDI+kzZ+ABuJOLeioTs7aAASqPi+gC8l5PTcLMAPADeiqDjkE2YAFyXH6NADaAF1PqA0GQEP8DHB2GDFABfAB8ihOZyQFyBoHuVymM3Y7whcHwjQQAPKwhSEOm4lhcBYjR2wgh6GcGAZTJZEPIuEg5QA-AD8PDkWj0SpcElJH8QOgFdYNAgAB4yfDUHTlFi1WrWORNYAAcX55Uw0FF2mgAAV0hVGphgAAxMlcc0ACXwGi0VvdbphFtyNtkCnNAFFJOhxLxoBNIjdrNLGtJFAa4OVQHEo8JeFd48I7qAPl8pTL-iAs9H+dJtHGKgXrOJyErkMBKzncLAa7HrgWu9pgAdMCRCIOSDmWPhpNAKiJgBCGr8AcCIV83pCyRTQAAiMOaYSihSgT3SaTbxh04SwgCsjHZuU5AIATKAkaUvmuN+SAduACq4JAAGkRzSDpz1AS9YSfO8OSwAEAGZX3fD911Jb8d2tfBhGkIJwMg58YIfODQEQt9VyLL8tz-ACIBETAEALPCfivAicFg2FSOQz80Ko-8kAAQSSEg5CY+lWPvaZiIARiQ8jUM3H8+NAABlQgWG0dhRJY0BoLYoioNkj8KJ4n8AFl8HKBjQAAIR0RdGi02EpMIyTYQAFkMlDKJ-WzaxxJ9HPE9iENfCFwSRdE-H-GoDW0Ap+RYGtQDkL16OSppJBIaQrIYhor0UWoNEiQ44BaL1NAACjrG4ATzet2AASiLCE0wzezcXGXt2GsezMGBczeGsWocPECqBtwHrLSaCqmoAKk6-Nut66xspoXgGtBABuBcgWsPkBWIe55PQ7cAHkkoAKQQXUJk00KIqAA)

### Intermediate/Advanced - Cutest Halloween Competition

Alright, this one definitelly took me a little bit. It was great to use the new template literal types, but I tried to rewrite the function using some functional programming aspects and didn't get very far. The start to my solution is by defining a new type `ID`

```ts
type ID = `${lowercase typeof breeds[number]}-${lowercase typeof costumes[number]}`
```

One thing I totally missed when I first read the 4.1 post was the `lowercase` operator (and the corresonding `uppercase`, `capitalize`, and `uncapitalize` ones too). The `ID` type uses the same array index type thing I've used in the previous challenges. It concatenates the two resulting unions of lowercase strings together into a template string seperated by a `-`.

Next, I modified the `const winners` declaration to use a type cast.

```ts
const winners = {} as Record<ID, ReturnType<typeof decideWinner>>
```

I did it this way since `winners` at the time of declaration isn't actually filled with values, but since the immediate following lines do that, its an okay type cast. Its for this exact reason that I tried rewriting the for loops so that the interpreter infers this record type automatically.

> If you can figure that out let me know! Would love to check out your algorithm.

Finally, I added one more type cast to the `id` declaration to complete my solution.

```ts
const id = `${breed}-${costume}`.toLowerCase() as ID
```

Check out my challenge solution [playground](https://www.typescriptlang.org/play?ts=4.1.0-beta&ssl=44&ssc=59&pln=34&pc=6#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)

## Challenge 5

Try it for yourself [here](https://dev.to/typescript/type-treat-challenge-5-40n4)

### Beginner/Learner - Generic trick or treating

I really appreciated this challenge, it is a great demonstration for [generics](https://www.typescriptlang.org/docs/handbook/generics.html). Generics go hand-in-hand with [DRY]() programming. In this challenge there are four houses. They all share the same object structure so we can first extrapolate the type into a shared `House` type.

```ts
type House = {
  doorNumber: number;
  trickOrTreat(): string;
  restock(items: string): void;
}
```

Now each house can be rewritten as an extension of this base type

```ts
type FirstHouse = House & {
	doorNumber: 1,
	trickOrTreat(): "book" | "candy";
  restock(items: "book" | "candy"): void;
}
// ...
```

But we're still repeating another part of these types, the return value of `trickOrTreat` and the argument of `restock`. Using a generic, we can make the `House` type even better.

```ts
type House<Items> = {
  doorNumber: number;
  trickOrTreat(): Items;
  restock(items: Items): void;
}
```

Now, each house type can be defined as
```ts
type FirstHouse = House<"book" | "candy"> & {
  doorNumber: 1;
};
// ...
```

The base `House` type passes its generic down to both methods.

Check out my challenge solution [playground](https://www.typescriptlang.org/play?#code/PTAEHUFNQCwQwG7TqAzpALqA9gM1tgK7qo4B2oKqGATpJqAO4wCWAxjAFAigDmLSUkSxs4ZACYBPShJxtRqFtjJwANqull640nQC01bGwDWLMrwB0nbmAAqrUi1JjK8wjTgZodAA510ZBieShR4oBgw0DBEJDagAEaEWBGQdADkzqCq2Fhh4oQ+quyekOKgbNji0GawqZAANDJlcZJETGLJ2OV0JZThkj7QzOww5USqZb7+kIGgcWLS0cSQVnEAkljZ2MakRcbQKeQa4azmTKwc5fDmkKTxmIz0ZHFLJKBOJ96Y7hQIaoTQMIAA1o7GMAHkaLYehggU04odcCwaNRQD44B4ALaYVI4fBA-wYIzGIEWOZxADCLlahFAyz6vBmqXY-UG4S6mLg+z6QIAEjFIHCMANoHFhpd4JlFOZVMgaLxCNjZrhsDRPqA6BgfqA-qoAXMeMDQSZIdDIJ44WIyod0Vj2aACbciSYgQB+azCtn85YAHg2kExqAAfKAALygADenFAoHE2FVADlFfcaAAuUBkZOpADc0fCNDBpphAAoAJTp-2B3MxwnE4ssLyBiuN1DlnXYFjiXMAXw9ItAADFkdRvegw6BR5AfQAieLx4zT0AAH1A09EEkk05DADJI3m44ms2nQABGHu5zie6AAZUgFQkk-Hk5nRJyMHiNGIMEXK+nmLMGCoFuoC7lGMYHjQSaYim6YAEznn2bL2Mi4iPuGz5rlam7LqucA+IUkA-qucaZoBwGgfu8aQUe6YAMwIZe-YDkQNARGhE4CjO65SER06oD487SPxnapEBO57uBVFQTBoAACwMVeoDXrQ9BYOGADaQ4ohgk6NLe96oQKjTITQhnLI0zHuGxAoALq5kAA)

### Intermediate/Advanced - Movie Night

> // You're part of a team scheduling a movie night, but someone accidentally
> // considered the movie "The Nightmare Before Christmas" to be a halloween 
> // movie, which it really isn't.

I'd like to point out that this statement is in fact **false**. [The Nightmare Before Chistmas]() is one of the best Halloween and Christmas movies since it can be enjoyed equally on both holidays. 🎃

But anyways, back to the challenge at hand, and oh is this one a doozy. I got the first part fairly easily, but the second part (kids schedule) was something else.

My solution makes use of many things previously used in these challenges, mapped types, template literals, and utility types. To kick off my solution I extracted the type from `moviesToShow` and defined a type for the schedule operations.

```ts
type Movies = typeof moviesToShow
type scheduleOps = 'getVHSFor' | 'makePopcornFor' | 'play'
```

Then using template literal types I create a `movieNames` type which capitalizes the keys of `Movies`.

```ts
type movieNames = `${capitalize keyof Movies}`
```

I combined this type with the `scheduleOps` to create another `ops` type. This `ops` type is then used in the utility type `Record` to define the `Schedule` type.

```ts
type ops = `${scheduleOps}${movieNames}`
type Schedule = Record<ops, () => void>
```

Inside of `makeScheduler`, I casted `schedule` to the `Schedule` type. Inside of the for loop, I casted the string operations to their corresponding types.

```ts
const schedule = {} as Schedule

// inside of the for loop
const capitalName = movie.charAt(0).toUpperCase() + movie.slice(1) as movieNames;

schedule[`getVHSFor${capitalName}` as ops] = () => {}
schedule[`makePopcornFor${capitalName}` as ops] = () => {}
schedule[`play${capitalName}` as ops] = () => {}
```

These template literal types are quite impressive!

The second part of the challenge requires doing something similar to the first, but this time filtering out any movies that is not `forKids`. I immediately tried to use `Extract` again but its not the right utility here. Instead I built a mapped-type based on the `Movies` type I created at the beginning.

```ts
type kidsMovies = {
  [K in keyof Movies]: Movies[K] extends { forKids: true } ? K : never
}[keyof Movies]
```

There is a lot of funny business going on here; lets break it down. First, the mapped expression is `K in keyof Movies`. This creates a union of all of the movies in `Movies` and assigns it to `K`. The value is another expression, `Movies[K] extends { forKids: true } ? K : never`. This expression checks if the type returned from `Movies[K]` satisfies the `forKids` truthiness. If it does, rather than returning that whole type, we just return the key `K` (and otherwise `never`). At the end of this type definition we have one last _trick_, we index using `keyof Movies`. In short, this applies every key from the original type `Movies` to the one just generated using mapped types. When one of these keys returns a `never` value (the falsy case of the mapped type expression), the interpreter leaves that value out of the resulting union. Thus, we are left with a union type of just the kids movies.

With this new list of movies in hand, I recreate the ops and schedule types and use them in the `makeKidScheduler` the same way I did previously

```ts
type KidsOps = `${scheduleOps}${capitalize kidsMovies}`
type KidsSchedule = Record<KidsOps, () => void>
```

Check out my challenge solution [playground](https://www.typescriptlang.org/play?ts=4.1.0-beta#code/PTAEE0HsFcHICcCmoAOBDeAXUkBmo1RNE0BbUAZwGMALRAE2gBsBLAOwHMDRTIA3FsjYsONTABpQAI2jYKkUokhtkaKlRb1EbTGiZMAngCgQoKsoqbESekTo9+g0ACIAKvYByIsaQzIAQoi4kEigAMI08CwUmL4UzkSQ0qqgNHpMkADuiNqgJmC8AoiSmTQstKAs2EjpBpUUbLCYAHRGRuZsMQ5FFK6QAMo0WaAAvKAA3kagqelZOWwAXBOgwfAA0poUS7h6FMgAvuJToMKisX4A8mwAokyk-ZhIiJhL4yshG-RbK7sHR9NDKjQCgABUgQO+b1Wn2+j2gf2OmDoAHUQjFkVVaK93utNks4QjphQmDkUAYABKQfRZbHQvE-Jh7UD7Iz7AgUMwWTBtTAGFDIACyjkQHLGvP5eG6gl6AyGmTapnciDqHEg2DQmTQdUyVRodmiRHgaD4It5ySoaGByAMMBmJsoCmQAAM0GwDE6Oew7Mh8pytFI0EzVmYaph2FwkchqHRGCT4M1QABBNigC5rMzpHHcXAseBddAUCiSGTc0zZBBRtJRTh+5A6pE4SN5ghsWyanSJZKgRAADxQrA0LR5fKjtAYzEQFxQotAsA4zwAauT+gAxEKwUAAH1nvgA1ogwShzPA2Gv4Bvt7B+1rYEZxchCoIPGQRaNQE6ACTjC0oKp6FgAF7IPuNr4EKPT7E6d4jjg05vp+4zRuOJJThQ+xfo+iDPooaFQfeoD9GOsbIGMABKiDHvQAA8kDTpIAAUACUowAHygHwkCaCxbS4NAbBUGGyg8Gg+6ETGE7wPRmHfOB0rMZM0wdF0SHEW+4xsoGBFEROxzBvRSnYJhlQphcUgAFYUS0IEUFJwoUIx8nHIpXIZr+uhMNhJFSogzS0BgiaYPRAAMjHNJgkAAKooPy8BhIGiBMaAADU3nNMS5QJQAjMxmmYZ5FAANxtNMRLaSSADaTrzpgS6riEX4-n+HkvpB7KwRQAC6b6JSMbHqU5lBlYglV7getHHqe9Xfmgbl6J5rWabRnXdcxvUTCyJWDeJFVOteBgNTNTXzU6bVLV1Yw9X1G3MsVoBIJg0AnltyGIKyCpgGEoavoQKkSe0Ll5d42BjKNYkvZJ0l9IMWSMe9oBKimmaRkGfECSwFgEKElrhb4YYWvodRUF9thGIDZzNNVtVnuSszZNoTGk8KXjk6Nh4TdTtPzAzZNiM0e009SdNsAzvoeGq3BpIL8zeQAhPDZQchQQzMLYUgpOYpC-nG3bwPAISM0UzO85Ty5nkb5xIIEqyIBEUQxHE3NM0DzSs+NISTfA5u+JbQQhDbkTROcNmwzzLR7V7fhW37tuBw7sMG0+zsm3V8CUhCYIQo7hvO67R7u9T4LAhnwJZ4n5P84XoKVyLcP9CgnFxhyrq2OgWCgAATDg+BIgafn6No86Y8gasZPKbTjxPphhEw8KgJlCbuL3kskpww-QCwTBfF33f2EwgbYMoxSgKU5R6jIG9b3guC+pGoB710h+SM8VDNK-E-v1PM-IO3C90EyMTVg4IYIgI5bC8X4oJFMbAXwcgtCmFAesAxSGAWrGYrYSQkzLLqbgKhMgrBIA9UIuA9bkFcCOQiUQUDYAACzNEyu-BhvoABUTCQQYGwJgTIkAWFwygNAO0yBb7mC0J2EIHBXSAUEfYCgL5nrEWLLIY+2DlDAN3JsbyFBWimAAJKYFgKrZAHBQyVHwDafh5gVYEGisA2+MjFAgP5ByAAtNIRRKjtR0ERt5X0aQORq1yFoKge8bCdlQYQNRW9MKtHwhEigslXxjAUqAcq6YvQgUlPEzqSxMkpK6r2YgrYORQg+PSAkzJQAAH5QDpiWCoE08BWTlXSWBOyHVoL8mqZsVC8Evy-RQtOdC01ZqsCAqAWJmTILtOQDCMGqkyIURCNRGEqEGKrTYhxLiPFUaQOEvuT4syJK2R6PiEckpIayhhhMY4Bk5ETjUhpDkMyhq6RCKAfSANhTGVTOZSyzRrJHLko5TapgAACmAKBOJEGwP2A0WD4ABSKKGcpyqYQ6s0OkXwgWbU5J0bAjV3KeTfFEvy8AArBVCuFKKMU4p7ESilKJ6UqBZUYkVAapVtrDSqouU2U18VzRaidTSyzpznTeWs9abLbk7VzuzXlh0CUCrasK5aF1xX9WxVKzle0DrDOOkqrpIqVqsQlZta6LJjj3UeimPpr1zWKk8cfB12QBEthwD8gSjrT6gCtByZGgiRwUH+risZmx4nmyJSJRA+yhoQzskimGtdlabx+BvIw4ynbk2ThzKW9NYbpuzuXPeFJOa5sTTAZNBZA35rLsbblKc05F2rnm0NGbeYyvziEBtVdM7Nq+GG52Fd05No-mAOuDdrBmC-n4xAo9GG+nnhAW0cC7qICcVaUAZj4BEEQJrPexA75VGsHoBxIoFHYDMScHIthCC4OEtFBgJ7Whzt9D-UAK52D0HDN6O6QNKD12wMGTdOLP2QOPQaR45RdwGDPd6LGSBGgcMgEkXwbpUCQEsJAwNpgSqlGsNaJdrpvVMkjKQJ9c7TAAGYEx8OEnUFQD7wpWP7HUNA9AQPo2gUwHFjw0DsHBZ2IDvh722HwsQhQ36cx5gPioIwQA)

## Conclusion

First, huge thank you to the TypeScript team for putting these challenges together. Even as a seasoned TS developer, I had a blast solving each beginner and intermediate challenge. I'd like to specially promote Gabrielle Crevecoeur's [finale post](https://dev.to/typescript/type-treat-the-finale-47a4) which has a great solution for the final challenge. Make sure you follow her on [dev.to](https://dev.to/gcrev93) and [twitter](https://twitter.com/NoWaySheCodes).

I understand my solutions might not be the **best** solutions, but thats the magic of programming. There is never just _one_ way to do things. Furthermore, I'd like to preface that your solutions (and the ones posted by the TS team) may be quite similar (if not the same) as mine. Everything I've presented here is my own work, and any similarities are purely coincidental.

I encourage you to try them out on your own and discuss your solutions with me in the comments below.

Happy Halloween! 🎃👻🕷