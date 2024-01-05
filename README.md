# Issue reproducer

There are 2 modules `mylibrary-a` and `mylibrary-b`. `mylibrary-a` depends on `mylibrary-b`.

`mylibrary-a` has the layout `sample_layout.xml`. Res id `icon` declared and used in the layout.  

`mylibrary-b` provides a transitive dependency on `androidx.recyclerview:recyclerview`.  
`androidx.recyclerview:recyclerview` also provides a transitive dependency on `androidx.core:core`.
In `androidx.core:core` there is also a declaration of a res id named `@+id/icon`, causing the plugin to give incorrect advice.

Run:

```shell
./gradlew :mylibrary-a:projectHealth
```

and you will get the following output:

```text
> Task :mylibrary-a:projectHealth
These transitive dependencies should be declared directly:
  implementation("androidx.core:core:1.7.0")
  implementation("androidx.customview:customview:1.0.0")
```

Run:

```shell
./gradlew :mylibrary-a:reason --id androidx.core:core
```

and you will get the following output:

```text
> Task :mylibrary-a:reason

----------------------------------------
You asked about the dependency 'androidx.core:core:1.7.0'.
You have been advised to add this dependency to 'implementation'.
----------------------------------------

Shortest path from :mylibrary-a to androidx.core:core:1.7.0 for debugCompileClasspath:
:mylibrary-a
\--- reproducer_980:mylibrary-b
      \--- androidx.recyclerview:recyclerview:1.3.2
            \--- androidx.core:core:1.7.0

Shortest path from :mylibrary-a to androidx.core:core:1.7.0 for debugRuntimeClasspath:
:mylibrary-a
\--- androidx.constraintlayout:constraintlayout:2.1.4
      \--- androidx.core:core:1.7.0

Shortest path from :mylibrary-a to androidx.core:core:1.7.0 for debugUnitTestCompileClasspath:
:mylibrary-a
\--- reproducer_980:mylibrary-b
      \--- androidx.recyclerview:recyclerview:1.3.2
            \--- androidx.core:core:1.7.0

Shortest path from :mylibrary-a to androidx.core:core:1.7.0 for debugUnitTestRuntimeClasspath:
:mylibrary-a
\--- androidx.constraintlayout:constraintlayout:2.1.4
      \--- androidx.core:core:1.7.0

Shortest path from :mylibrary-a to androidx.core:core:1.7.0 for debugAndroidTestCompileClasspath:
:mylibrary-a
\--- reproducer_980:mylibrary-b
      \--- androidx.recyclerview:recyclerview:1.3.2
            \--- androidx.core:core:1.7.0

Shortest path from :mylibrary-a to androidx.core:core:1.7.0 for debugAndroidTestRuntimeClasspath:
:mylibrary-a
\--- androidx.constraintlayout:constraintlayout:2.1.4
      \--- androidx.core:core:1.7.0

Shortest path from :mylibrary-a to androidx.core:core:1.7.0 for releaseCompileClasspath:
:mylibrary-a
\--- reproducer_980:mylibrary-b
      \--- androidx.recyclerview:recyclerview:1.3.2
            \--- androidx.core:core:1.7.0

Shortest path from :mylibrary-a to androidx.core:core:1.7.0 for releaseRuntimeClasspath:
:mylibrary-a
\--- androidx.constraintlayout:constraintlayout:2.1.4
      \--- androidx.core:core:1.7.0

Shortest path from :mylibrary-a to androidx.core:core:1.7.0 for releaseUnitTestCompileClasspath:
:mylibrary-a
\--- reproducer_980:mylibrary-b
      \--- androidx.recyclerview:recyclerview:1.3.2
            \--- androidx.core:core:1.7.0

Shortest path from :mylibrary-a to androidx.core:core:1.7.0 for releaseUnitTestRuntimeClasspath:
:mylibrary-a
\--- androidx.constraintlayout:constraintlayout:2.1.4
      \--- androidx.core:core:1.7.0

Source: debug, main
-------------------
* Uses 1 resource: AttrRef(type=id, id=icon) (implies implementation).

Source: release, main
---------------------
* Uses 1 resource: AttrRef(type=id, id=icon) (implies implementation).

Source: debug, test
-------------------
* Uses 1 resource: AttrRef(type=id, id=icon) (implies testImplementation).

Source: release, test
---------------------
* Uses 1 resource: AttrRef(type=id, id=icon) (implies testImplementation).

Source: debug, android_test
---------------------------
* Uses 1 resource: AttrRef(type=id, id=icon) (implies testImplementation).
```