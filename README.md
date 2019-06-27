# libuuidpp - A lightweight C++ UUID class.

A lightweight C++ class wrapper around the `libuuid` library.

## Features

* Compatible with std containers such as `std::vector`, `std::set`, `std::map` etc.
* Handles upper and lower case uuids.
* Automatically handles Microsoft-formatted uuids `{D1ABE846-63D3-4C0A-89EF-68F1A306F6D3}`
* Multiple formats for output strings (uppercase, lowercase, bracketed).
* Equality and greater/lesser comparators.

## Why?

I wanted a very simple lightweight UUID class wrapper without having to import boost or some other large framework that would be compatible with the standard library (eg. `std::vector`, `std::map`).  I also didn't want to write the UUID generation myself, as here be dragons (especially relating to privacy).  libuuid is a well defined and tested generator.

`libuuid` is installed by default on macOS and is an easy install for other platforms.

## Usage

Just add `libuuidpp/libuuidpp.hpp` to your project. Requires `libuuid` and standard C++ libraries (compatible back to C++11, tested up to C++17).

You can find a bunch of tests in `main.cpp`.

## API classes

### `libuuidpp::uuid`

The uuid class

### `libuuidpp::uuid::formatting`

A typesafe enum bitfield for describing string output.

### `libuuidpp::uuid::binary`

A class used to transport raw binary data in and out of the uuid object.

## Examples

### Simple creation
```cpp
// The standard string constructor throws an exception if the guid is invalid
libuuidpp::uuid uid1("D1ABE846-63D3-4C0A-89EF-68F1A306F6D3");
libuuidpp::uuid uid2("d1abe846-63d3-4c0a-89ef-68f1a306f6d3");
libuuidpp::uuid uid3("{D1ABE846-63D3-4C0A-89EF-68F1A306F6D3}");
```

### Creating random UUIDs and nil UUID.
```cpp
// Create a random uuid
auto random_uid = libuuidpp::uuid::random();
assert(!random_uid.is_nil());

// nil uuid (statically defined)
const auto& nil_uid = libuuidpp::uuid::nil;
assert(nil_uid.is_nil());
```

### Binary handling
```cpp
/// Create a uuid object from raw binary data
libuuidpp::uuid::binary binaryData {
   0xc0, 0x6c, 0x89, 0x2b, 0x50, 0xab, 0x45, 0x85, 0x98, 0x19, 0x5f, 0x4b, 0xa3, 0xb8, 0xf6, 0x9b
};
libuuidpp::uuid uuid(binaryData);

/// Get the data back out
const libuuidpp::uuid::binary roundTrip = raw.data();
```

### Setting

#### Constructor throws exceptions for invalid uuids
```cpp
try {
   libuuidpp::uuid my_dodgy_uuid("F211F534-8DFB-4269-9A bad");
	
   // Do something with my_dodgy_uuid
	
} catch (libuuidpp::exception::invalid_uuid e) {
   printf("Successfully caught exception during creation\n");
}
```
#### Basic setters
```cpp
libuuidpp::uuid myUUID;
bool didSet = myUUID.set("F211F534-8DFB-4269-9AF1-245FA0AB8D87");
assert(didSet == true);

/// Attempting assignment to an invalid uuid string
didSet = myUUID.set("D1ABE846-63D3-4C0A-89EF-");
assert(didSet == false);
assert(myUUID.is_nil());

/// Set myUUID to a random guid
myUUID.set_random();
assert(!myUUID.is_nil());

/// Assigning a new uuid
auto newUUID = libuuidpp::uuid("D1ABE846-63D3-4C0A-89EF-68F1A306F6D3");
myUUID = newUUID;
assert(myUUID == newUUID);
```
### Generate a string from the uuid
```cpp
libuuidpp::uuid uid1("D1ABE846-63D3-4C0A-89EF-68F1A306F6D3");

// Default output (uppercase, no brackets)
std::string uidString1 = uid1.str();
assert(uidString1 == "D1ABE846-63D3-4C0A-89EF-68F1A306F6D3");

// Lowercase output
std::string uidString2 = uid1.str(libuuidpp::uuid::formatting::lowercase);
assert(uidString2 == "d1abe846-63d3-4c0a-89ef-68f1a306f6d3");

// Bracketed output
std::string uidString3 = uid1.str(libuuidpp::uuid::formatting::brackets);
assert(uidString3 == "{D1ABE846-63D3-4C0A-89EF-68F1A306F6D3}");

// Lowercase, Bracketed output
std::string uidString4 = uid1.str(libuuidpp::uuid::formatting::lowercase | libuuidpp::uuid::formatting::brackets);
assert(uidString4 == "{d1abe846-63d3-4c0a-89ef-68f1a306f6d3}");
```
### Comparison operators
```cpp
libuuidpp::uuid uid1("d1abe846-63d3-4c0a-89ef-68f1a306f6d3");
libuuidpp::uuid uid2("{D1ABE846-63D3-4C0A-89EF-68F1A306F6D3}");
libuuidpp::uuid uid3("84AEEDE7-E056-4F89-BAD1-BF97B96FAAAA");

/// uid1 and uid2 resolve to the same uuid value, therefore they are equal
assert(uid1 == uid2);

/// uid2 and uid3 are different uuids, so they aren't equal
assert(uid2 != uid3);
```
### Lexigraphic sorting support
```cpp
std::vector<libuuidpp::uuid> result;
for (size_t c = 0; c < 10; c++) {
   result.push_back(libuuidpp::uuid::random());
}
std::sort(result1.begin(), result1.end(), std::less<libuuidpp::uuid>());
std::sort(result1.begin(), result1.end(), std::greater<libuuidpp::uuid>());
```
## Thanks

Typesafe enum bitfield support from Just Software Solutions.

 - See [https://www.justsoftwaresolutions.co.uk/cplusplus/using-enum-classes-as-bitfields.html](https://www.justsoftwaresolutions.co.uk/cplusplus/using-enum-classes-as-bitfields.html)

## License

```
Boost Software License - Version 1.0 - August 17th, 2003

Permission is hereby granted, free of charge, to any person or organization
obtaining a copy of the software and accompanying documentation covered by
this license (the "Software") to use, reproduce, display, distribute,
execute, and transmit the Software, and to prepare derivative works of the
Software, and to permit third-parties to whom the Software is furnished to
do so, all subject to the following:

The copyright notices in the Software and this entire statement, including
the above license grant, this restriction and the following disclaimer,
must be included in all copies of the Software, in whole or in part, and
all derivative works of the Software, unless such copies or derivative
works are solely in the form of machine-executable object code generated by
a source language processor.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE, TITLE AND NON-INFRINGEMENT. IN NO EVENT
SHALL THE COPYRIGHT HOLDERS OR ANYONE DISTRIBUTING THE SOFTWARE BE LIABLE
FOR ANY DAMAGES OR OTHER LIABILITY, WHETHER IN CONTRACT, TORT OR OTHERWISE,
ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER
DEALINGS IN THE SOFTWARE.
```
