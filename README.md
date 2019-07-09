# useful-frida-snippets
Useful Android Frida code snippets. (Utili frammenti di Frida per androidi)

Some of the snippets aren't made by me. Credits goes to the authors.

<details>
<summary>Basic js frida script template</summary>

~~~js
Java.perform(function() {

	// code goes here

    console.log("Done.");
});

~~~

</details>

---

<details>
<summary>Print class members and methods</summary>

~~~js
console.log('Loaded class members and methods', Object.getOwnPropertyNames(Java.use('com.example.SomeClass').__proto__).join('\n\t'));
~~~

</details>

<details>
<summary>Print webview loaded url</summary>

~~~js
Java.use("android.webkit.WebView").loadUrl.overload("java.lang.String").implementation = function (s) {
    console.log('webview loaded url = ', s.toString());
    this.loadUrl.overload("java.lang.String").call(this, s);
};
~~~

</details>


<details>
<summary>Get application context</summary>

~~~js
function getApplicationContext() {
  return Java.use('android.app.ActivityThread').currentApplication().getApplicationContext().getContentResolver();
}
~~~

</details>

<details>
<summary>Print application android_id</summary>

~~~js
function logAndroidId() {
  console.log('android_id = ', Java.use('android.provider.Settings$Secure').getString(Java.use('android.app.ActivityThread').currentApplication().getApplicationContext().getContentResolver(), 'android_id'));
}
~~~

</details>

<details>
<summary>Print shared preferences updates</summary>

~~~js
var shared_pref_class = Java.use('android.app.SharedPreferencesImpl$EditorImpl');

shared_pref_class.putString.overload('java.lang.String', 'java.lang.String').implementation = function(k, v) {
    console.log('Shared preference updated: ', k, '=', v);
    return this.putString(k, v);
}

shared_pref_class.putInt.overload('java.lang.String', 'int').implementation = function(k, v) {
    console.log('Shared preference updated: ', k, '=', v);
    return this.putInt(k, v);
}


shared_pref_class.putFloat.overload('java.lang.String', 'float').implementation = function(k, v) {
    console.log('Shared preference updated: ', k, '=', v);
    return this.putFloat(k, v);
}

shared_pref_class.putBoolean.overload('java.lang.String', 'boolean').implementation = function(k, v) {
    console.log('Shared preference updated: ', k, '=', v);
    return this.putBoolean(k, v);
}

shared_pref_class.putLong.overload('java.lang.String', 'long').implementation = function(k, v) {
    console.log('Shared preference updated: ', k, '=', v);
    return this.putLong(k, v);
}

shared_pref_class.putStringSet.overload('java.lang.String', java.util.Set).implementation = function(k, v) {
    console.log('Shared preference updated: ', k, '=', v);
    return this.putStringSet(k, v);
}
~~~



</details>

<details>
<summary>Create java array</summary>

~~~js
var byteArr1 = Java.array('byte', [ 13, 37, 42 ]);
~~~

</details>

<details>
<summary>Get hex string from byte array</summary>

~~~js
function byteArrayToHexString(array, size) {
    if (array == null) return 'null';

    var result = [];
    for (var i = 0; i < size; ++i) {
        result.push(('0' + (array[i] & 0xFF).toString(16)).slice(-2));
    }
    return result.join('');
}

byteArrayToHexString(byteArr1, byteArr1.length);
~~~

</details>

<details>
<summary>Get ascii string from byte array</summary>

~~~js
function byteArrayToAscii(array, size) {
		if (array == null) return 'null';

	    var result = [];
	    for (var i = 0; i < size; ++i) {
	        result.push(String.fromCharCode(
	            parseInt(
	                ('0' + (array[i] & 0xFF).toString(16)).slice(-2),
	                16
	            )
	        ));
	    }
	    return result.join('');
	}
    
byteArrayToAscii(byteArr1, byteArr1.length);
~~~

</details>

<details>
<summary>Print secret crypto keys bytes</summary>

~~~js
function byteArrayToHexString(array, size) {
    if (array == null) return 'null';

    var result = [];
    for (var i = 0; i < size; ++i) {
        result.push(('0' + (array[i] & 0xFF).toString(16)).slice(-2));
    }
    return result.join('');
}

var SecretKeySpec_class = Java.use('javax.crypto.spec.SecretKeySpec');

SecretKeySpec_class.$init.overload('[B', 'java.lang.String').implementation = function(p0, p1) {
    console.log('SecretKeySpec =', byteArrayToHexString(p0, p0.length), 'algo =', p1);
    return this.$init(p0, p1);
};

SecretKeySpec_class.$init.overload('[B', 'int', 'int', 'java.lang.String').implementation = function(p0, p1, p2, p3) {
    console.log('SecretKeySpec =', byteArrayToHexString(p0, p0.length), 'offset =', p1, 'size =', p2, 'algo =', p4);
    return this.$init(p0, p1, p2, p3);
};
~~~

</details>

<details>
<summary>Print all strings created at runtime</summary>

~~~js
['java.lang.StringBuilder', 'java.lang.StringBuffer'].forEach(function(clazz, i) {
  var func = 'toString';
  Java.use(clazz)[func].implementation = function() {
    var ret = this[func]();
    console.log('String created: ' + ret);
    return ret;
  }   
}); 
~~~

</details>

<details>
<summary>Print stacktrace in this point</summary>

~~~js
Java.perform(function() {
    var jAndroidLog = Java.use("android.util.Log"), jException = Java.use("java.lang.Exception");
    console.log(jAndroidLog.getStackTraceString( jException.$new()));
}); 
~~~

</details>


TODO:

Add things from 

* https://gitlab.com/roxanagogonea/frida-scripts/blob/master/data-storage/sqlite-database.js
* https://gitlab.com/roxanagogonea/frida-scripts/blob/master/data-storage/log.js
* https://gitlab.com/roxanagogonea/frida-scripts/blob/master/network/http-connection.js
* https://gitlab.com/roxanagogonea/frida-scripts/blob/master/network/read-write.js
* https://gitlab.com/roxanagogonea/frida-scripts/blob/master/network/ssl-pinning.js
