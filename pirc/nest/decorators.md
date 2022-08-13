---
title: Typescript Decorators and Metadata
date: 2022-08-13T17:22:51Z
---

Typescript Decorators and Metadata
---

While learning about NestJS, there was something in particular that caught my attention. Any class in nest that has the `@Injectable` decorator can have dependencies injected into it. The crazy thing is that in one of these classes, you can specify a parameter and it's type in the constructor, and nest is able to use the type as a token and automatically inject the appropriate dependency. Here's an example:

```Typescript
import UserRepo from '...';

@Injectable()
export class UserService {
  constructor(
    // nest detects the type of this parameter
    // and uses the type name as a token to identify
    // which dependency to inject.
    private _userRepo: UserRepo
  ) {}
}
```

To me this seemed like magic. How tf does nest do this? It turns out it's with **decorators and metadata**. I was interested in how they did this because I wanted to make a nest module for Cassandra that works similarly (although the details of that are for another devlog). The basics of decorators are in the official [documentation](https://www.typescriptlang.org/docs/handbook/decorators.html), so there's no use repeating that. However, I'm going to note down some things that weren't immediately apparent, and provide a basic pseudo-implementation of how I think nest scans classes when it's preparing to inject dependencies.

Oh, and here's a really helpful article that also helped me understand this stuff: https://itnext.io/advanced-nestjs-techniques-part-1-custom-decorators-aa6d7f85c5b6

## Weird things

### 1. You have to install `reflect-metata` (if you want to use it)

I thought this was kind of weird, considering that the Typescript compiler actually adds certain metadat automatically using this library, but only if you have installed. Why don't they just bake it into Typescript? I don't know.

So install it as usual:
```bash
npm install reflect-metadata
```

And then import it like so
```Typescript
import 'reflect-metadata';

// now you have access to the `Reflect` object or namespace or whatever this is
Reflect.getMetadata(...);
```

### 2. The `target` param for a class decorator is not the same as the `target` param for method or property decorators

In the Typescript docs, they go over how there are 5 types of decorators:
- Class decorators
- Method decorators
- Accessor decorators
- Property decorators
- Parameter decorators

For my use case, I was mainly interested in class, method, and property decorators. So I know what I'm about to say applies to those, but I'm not 100% sure about accessor and parameter decorators (I would imagine it does though).

You can create a class decorator to print out the class name (which could be used as a token):
```Typescript
function classDec() {
  // `target` is the constructor for the class
  return function (target: any) {
    console.log('class name:', target.name)
  }
}

@classDec()
class TestClass {}
```
When you run this code, it will print out `class name: TestClass`.

However, if you try to write a method decorator that does the same thing, you'll be surprised that it doesn't work:
```Typescript
function methodDec() {
  return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
    // `target` is an empty object?!
    // this prints "class name: undefined"
    console.log('class name:', target.name)
  };
}

class TestClass {

  @methodDec()
  someMethod () {}
}
```
I'm not entirely sure why, but `target` in this case is an empty object. So you have to access the constructor by using `target.constructor`. Fortunately, that gives you the ability to retrieve the class name.

```Typescript
// prints "class name: TestClass"
console.log('class name:', target.constructor.name)
```

## Getting constructor parameter types using a class decorator

According to the [docs](https://www.typescriptlang.org/docs/handbook/decorators.html#metadata), it turns out that as long as you have `"experimentalDecorators": true` and `"emitDecoratorMetadata": true` set in your tsconfig, Typescript will automatically add some metadata to the class. The example they give is with method decorators, but I found that with class decorators, it sets the `design:paramtypes` metadata that has the types of the parameters for the constructor. So we can get the names of the types of all the constructor parameters with the following code:

```Typescript
function PrintConstructorParamTypes() {
  // `target` is the constructor for the class
  return function (target: any) {
    console.log('class name:', target.name);

    // get the parameter types using the `design:paramtypes` metadata
    // key and passing the constructor of the class
    const paramTypes: any[] = Reflect.getMetadata('design:paramtypes', target);
    const paramTypeNames: string[] = paramTypes.map((paramType: any) => paramType.name);
    console.log('param types:', paramTypeNames);
  }
}

class Dep {}

@PrintConstructorParamTypes()
class TestClass {

  constructor (arg1: Dep, arg2: string) {}
}
```

This prints out
```
class name: TestClass
param types: ["Dep", "String"]
```

You can imagine then, that using this method, nest associates each class name with its dependencies, and is able to call `new ClassName()` with those dependencies when it's going through its initialization sequence. 