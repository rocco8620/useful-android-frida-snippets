# useful-frida-snippets
Useful Android Frida code snippets. (Utili frammenti di Frida per androidi)

Some of the snippets aren't made by me. Credits goes to the authors.

<details>
<summary>Print class members and methods</summary>
~~~
console.log(Object.getOwnPropertyNames(Java.use('com.example.SomeClass').__proto__).join('\n\t'));
~~~
</details>