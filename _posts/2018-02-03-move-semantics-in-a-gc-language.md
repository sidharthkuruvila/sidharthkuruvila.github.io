---
layout: post
title: Move semantics in a gc language
---
I’ve been learning c++ lately. The thing that kept me away from the language for a long time is the lack of automatic memory management. As a java programmer you get used to certain luxuries and not worrying about whether you are leaking memory is one of them.

The garbage collector doesn’t manage all resources though, which is why we still have to explicitly close open files. This is quite easy to do thanks to the try with resource pattern available in both kotlin and java. 

(I’m going to use kotlin because the syntax is simpler, but the examples should be easily translated to java)

```kotlin
FileInputStream("file.txt").use {
    println("char = " + (char)it.read());
}
//The file object is no longer in scope.
```


However, there are limitations to this approach, the scope of the file object is delimited by curly braces. And it is not possible to pass the function object beyond the curly braces without bad things happening. You get used to living without the ability.

C++ runs into similar problems of resource management, in c++’s case it applies to any object allocated on the heap. And c++ has a similar solution to the problem in the form of a pattern called [RAII](https://en.wikipedia.org/wiki/Resource_acquisition_is_initialization). With RAII an object on the stack will call its own destructor method before going out of scope.

```c++
class Resource {
   int n;
public:
   Resource(int n): n(n) {};
   ~Resource(){
       cout << "Deleting resource instance " << n << endl;
   }
};

void resource_demo() {
   Resource res1(1);
   {
       Resource res2(2);
       cout << "This is before closing the code block" << endl;
   }
   cout << "This is just after the code block" << endl;
}
```

Ofcourse RAII has the same limitations of delimited scoping as the try with resource pattern. C++ 11 introduces a construct called move semantics to get around this.

```c++
void resource_consumer(unique_ptr<Resource> resource_ptr){
   cout << "Ownership of the resource transferred to resource_consumer" << endl;
   cout << "The resource will be deleted on return of the method" << endl;
}

void ResourceDemo::resource_demo2() {
   unique_ptr<Resource> resource_ptr(new Resource(1));
   resource_consumer(move(resource_ptr));
   cout << "This after the method call" << endl;
}
```

What happens is that the ownership of the unique pointer is transferred to the function resource_consumer and it deletes itself when that function exits.

So what would move semantics look like on a jvm language? 

This can be done by introducing a new class ```Movable<T>``` that demonstrates semantics similar to RAII for variables using the close method instead of a destructor. 

The c++ example can then be written as.

```kotlin
fun resource_consumer(resource: Movable<Resource>){
   println("The resource will close on return");
}

fun demo1(){
   var resource = movable(Resource());
   resource_consumer(resource);
   println("This after close");
   //Compile error if resource is use here
   //println(resource);
}
```
Methods should also be able to return movable resources
```kotlin
fun resource_provider(): Movable<Resource> {
   val resource = movable(Resource())
   println(resource.get());
   return resource
}

fun demo2() {
   var resource = resource_provider()
   println(resource.get())
   println("The resource will close on return")
}
```

Movable fields are a bit tricky, it would be possible to automatically rewrite the close method in a class with movable fields to close them when the object itself is closed, but there could be ordering issues.

Overloading class behaviour the way movable works might make code harder to read, on the other hand introducing a new keyword would simply clutter the syntax.

Another possible extension in the same vein would be ```Sharable<T>``` to add implicit reference counted resources.
