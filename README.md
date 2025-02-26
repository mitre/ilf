# MITRE Intermediate Log Format (ILF)
For real-time analysis of cyber events, it is necessary to process event records from an event stream as quickly as possible to facilitate detection of adversary actions. Some techniques for speeding up the detection are partial interpretation of an event record, and applying regex to the event stream. [MITRE](https://www.mitre.org/) developed Intermediate Log Format (ILF) format to enable such techniques. 

## ILF Record Structure

1. An ILF record (referred to simply as as “record” below) MUST have the following structure.
```
<event type>[<sender>,<receiver>,<time stamp>,(<attribute fields>)] 
```
2. Every record ends with a space, inserted after the closing square brace “]”. Additional whitespace, such as newlines, can also follow the space.
3. The `<event type>`, `<sender>`, and `<receiver>` fields are interpreted as strings, and are not required to be in quotes.
    - An `<event type>` MUST consist of only alphanumeric characters or “_”.
    - The `<sender>` and `<receiver>` fields can contain alphanumeric characters and the characters ‘.’, ‘*’and ‘_’. The dot (`.`) character is used to specify hierarchy of components of the sender or receiver, e.g., car.brake
    - The `<time stamp>` field MUST be compliant to these standards: RFC 3339 or UNIX epoc. All `<time stamp>` fields in a stream of records must follow the same standard. The timestamp field can be empty. 
4. The fields, `<sender>`, `<receiver>`, and `<time stamp>`, are located at the first, second, and third locations, respectively. 
    - These fields may be empty, though the comma separators of the fields MUST be present. e.g., `OCSF_100400[,,,()] ` is a valid ILF record.
    - If the `<sender>` or `<receiver>` values are not known or are irrelevant, their values can be *. 
    - If the record is broadcast, then the `<receiver>` field is *.
5. The fourth and last field of an ILF record, `<attribute fields>`, contains semicolon-separated key-value pairs with the structure `<attribute_name>=<attribute_value>`
    - `<attribute fields>` can be empty, though the enclosing parentheses must remain.
    - An attribute name may contain alphanumeric characters, “.”, or “_”
    - `<attribute_value>` can be empty in some records in an event stream, as long as `<attribute_name>=` is specified. 
    - `<attribute_value>` is interpreted based on the event and component structure definitions provided to the ILF consumer.
    - When an `<attribute_value>` is a string, it is enclosed in double quotes, and the string content within the quotes must be escaped appropriately and conform to Java string escape conventions.
    - The key-value pairs need not follow the same location order in different ILF records in the same event stream.
6. The values of the attributes in the fourth field can be a String, Number, Array, Dictionary, or Time, as defined below.
    - Strings
        - Strings may be enclosed in single quotes or double quotes.
        - Strings may be encoded as UTF-8, UTF-16, or UTF-32 characters, depending on what the ILF consumer expects.
        - All other character escapes are ignored by the ILF consumer and passed through. (e.g. \n is kept as \n, not turned into a newline.)
    - Numbers
        - Integer, long, double, including signed prefixes (e.g., 2.1, -17, -17.0, 17l). 
        - Base-10 float, including signed exponents (e.g., 10.3e-3). 
        - Binary numbers are prefixed with 0b (e.g., 0b1100).
        - Octal numbers are prefixed with 0o (e.g., 0o723).
        - Hex numbers are prefixed with 0x (e.g., 0x93ABCDEF).
    - Arrays
        - Arrays are sequences of numbers, separated by commas, surrounded with square brackets (e.g., [1,0, 45, 6 ], [0x93AB,0x1F] ).
        - Only Numbers and Strings are allowed in arrays.
        - Whitespace is allowed around the array values.
    - Dictionaries
        - Dictionaries are sequences of key value pairs matched with colons, separated by commas, surrounded with curly brackets (e.g. {“key”: 4, “key2”:6.1 , “8”:677 }).
        - Keys in dictionaries MUST be strings.
        - The types of values in dictionaries can only be Strings or Numbers.
        - Whitespace is allowed around the keys or values.
    - Time
        - Specified based on the RFC 3339 or Unix epoc standard. 
    - Enums
        - Enums can only contain alphanumeric characters, or ‘_’ and '.’.
          
If the value of an attribute cannot be parsed as a String, Number, Array, Dictionary, or Time, an attempt will be made to parse it as an enum. A failure to parse may be flagged an error, or ignored.

## ILF Translator Implementations
1. [Sysmon XML to ILF Translator](https://github.com/mitre/event-xml-to-ilf)
2. [OCSF events to ILF mapping rules](https://github.com/ocsf/examples/tree/main/encodings/ilf) and a prototype translator [OCSF events to ILF Translator](https://github.com/mitre/ocsf_ilf_translator)
3. [ILF Translator Support Library](https://github.com/mitre/lib_ilf)

## Public Release Information
> [!NOTE]
> Approved for Public Release; Distribution Unlimited. Public Release Case
> Number 24-00845-18

(c) 2024-2025 The MITRE Corporation. All rights reserved.
