<p align="center">
  <picture>
    <img class="top-logo" alt="mobject main logo" src='./images/logo-light.svg'>
  </picture>
</p>

# Coding Convention

> Clean code is simple and direct. Clean code reads like well-written prose. Clean code never obscures the designer’s intent but rather is full of crisp abstractions and straightforward lines of control. - Grady Booch, author of Object Oriented Analysis and Design with Applications”

## Opening Statement

This coding convention has been created to help keep our code base clean and tidy. It will act as a guide for those of you who have not settled on a standard, and will impose rules on those of you who wish to contribute.

## Naming

### Intent

Use names which both reveal your intent and removes the need for supplementing your code with comments.

```example
// Put the UPS in to full capacity mode if the Power Supply 10 is in fault
IF SUP_10.flt THEN
	UPS.smode := 1;
END_IF
```

The example above has been renamed and refactored to remove the comment. This allows us to read the code without placing any mental obstacles in the way of our understanding.

```example
IF PowerSupply10.HasFault THEN
	UPS.mode := FULL_CAPACITY_MODE;
END_IF
```

### Avoid abbreviations

Where possible, do not abbreviate. This forces us to think, decode and remember a term. Every time we give our brain something to do it forces it to work harder. Instead favour whole words and phrases so that our mind can stay on auto-pilot when reading our code.

```example
VAR strBtn : BOOL;
```

It may seem only a small step, but the brain power that we have saved not decoding an abbreviation can be used for other more creative or fault finding tasks.

```example
VAR startButton : BOOL;
```

### Pointers

When naming pointers, it's essential to include a lowercase 'p' prefix in their symbol names. This practice aids in quickly identifying pointers, as they are unique types that require dereferencing.

```example
VAR pCount : POINTER TO INT;
// pCount^ := 123;
```

### PVOID

The PVOID type should adhere to the general symbol naming guidelines applicable in its context. Nevertheless, incorporating the term "Address" within the symbol name is considered a best practice for clarity.

```example
VAR sourceAddress : PVOID;
```

### Variable Alignment

This style guide is designed to be as simple as possible, therefore the same has been applied to aligning variables. You will see in the example below that a neat layout is formed by using only standard tab indentation and spaces.

Four core benefits are realized,

- It's fast.
- Refactoring using Find / Replace will not destroy the layout if variable names change in length.
- There is no requirement for collaborators to install custom tools to automate alignment.
- It looks nice on all fonts.

Variables must be aligned using the following conventions.

- Begin each line with a tab character (not spaces).
- Use a single space between each section: name, type, and (if present) address or initializer.
- End each line with a semicolon, with no trailing space before it.

```example
VAR
	exampleBool : BOOL := TRUE;
	exampleInput AT %I* : INT;
	exampleArray : ARRAY [0..n] OF REAL;
	counter : Counter(StartValue := 123, EndValue := 456);
END_VAR
```

### Multi-Line Initializers

Classes with multiple initializers may be broken across multiple lines for readability.

- The opening parenthesis must appear immediately after the class name.
- Each parameter appears on its own line, indented with a tab.
- A comma follows each parameter except the last.
- The closing parenthesis and semicolon appear on a new line, aligned with the variable name.

```example
VAR
	tcpIpClient1 : TcpIpClient(
		ServerAddress := '192.168.0.1',
		ServerPort := 123,
		ConnectionTimeout := T#5s,
		KeepAlive := TRUE
	);
	tcpIpClient2 : TcpIpClient(
		ServerAddress := '192.168.0.2',
		ServerPort := 456,
		ConnectionTimeout := T#5s,
		KeepAlive := TRUE
	);
END_VAR
```

The same may also be applied to Function Block calls within the program.

## Comments

Ask yourself 'why do I need a comment?'

If you need them to convey your code's true meaning then you should refactor and rename your variables to make your [Intent](?id=intent) clear.

Do not use comments for auditing purposes, tracking changes, or attributing authorship. This adds extra work to the coders who follow you. Favour source control instead.

**Comments must not be used to,**

- explain how your code works (Rename / Refactor)
- comment out old code (Delete it and use Source Control)
- add author information (use Source Control)

**Comments may be used**

- as place holders or for general information.

```example
IF PowerSupply10.HasFault THEN
	// Custom fault reaction code may go here...
END_IF
```

Remember, no comments are the best form of comments!

## Enumeration

### Global Enumeration

Global Enumeration should be kept to a minimum as this will cause tight coupling between objects which use them. Tight coupling is a bad thing. Try instead to replace global enumeration with other objects which can be passed around.

### Local Enumeration

Enumeration inside of a class is good and will make CASE statements easier to use and extend. Use inline enumeration as shown below. This keeps internal state locked away inside a class and prevents us needing to manage this enumeration in a second file.

Enumeration must be ALL_CAPITALS with underscore word separation.

```declaration
FUNCTION_BLOCK Cylinder
VAR
	state : (RETRACTED, EXTENDING, EXTENDED, RETRACTING, JAMMED_EXTENDING, JAMMED_RETRACTING);
END_VAR
```

```body
CASE state OF
	RETRACTED:
		//
	EXTENDING:
		//
//...
END_CASE
```

## Constants

Constants must be ALL_CAPITALS with underscore word separation.

Replace "Magic numbers" with constants to assist with readability and understanding.

```example
VAR CONSTANT
	MAXIMUM_BUFFER_SIZE_IN_BYTES : UDINT := 200;
END_VAR
```

As with all symbol names, a well-named constant should convey its purpose and context clearly.

Consider the examples below:

```example
VAR CONSTANT
	(* Less Clear *)
	BUTTON_DEBOUCE_DURATION : UDINT := 3; // in milliseconds

	(* More Clear *)
	BUTTON_DEBOUCE_DURATION_IN_MS : UDINT := 3;
END_VAR
```

The first example, BUTTON_DEBOUCE_DURATION, required the use of a comment for clarity, which is not ideal as comments can become outdated or overlooked. The second example, BUTTON_DEBOUCE_DURATION_IN_MS, includes the unit of measurement in the name itself, making it immediately clear and informative without needing additional explanation.

## Class

In the IEC standard, there is a keyword for "Class". Favour the use of CLASS POUs whenever possible. If you are unable to define a CLASS POU, consider using a Function Block with the attributes outlined below. Ensure you decorate your classes with the no_explicit_call attribute to prevent them from being used as standard IEC Function Blocks.

```example
{attribute 'no_explicit_call' := 'This FB is a CLASS and must be accessed using methods or properties'}
```

!>You must not place code in the FUNCTION_BLOCK body. Use a public method instead, such as .CyclicCall();

!>You must not allow VAR_INPUT, VAR_OUTPUT and VAR_IN_OUT to exist in a class declaration.

### Naming

Always use **PascalCase** for class names. You should use noun or noun phrase for class names. Do not use prefixes. An underscore may be used if the class is typed and it assists with overall the readability.

```example
FUNCTION_BLOCK AnalogValue_LREAL EXTENDS AnalogValue
// VAR_INPUT, VAR_OUTPUT and VAR_IN_OUT is not permitted here.
VAR

END_VAR
```

### Private Variables

Private variables must be **camelCase**.

Private variables which share the same name as a Property must be prefixed with an underscore. Try to avoid this type of name clash where possible.

### Example

```declaration
{attribute 'no_explicit_call' := 'This FB is a CLASS and must be accessed using methods or properties'}
FUNCTION_BLOCK Cylinder EXTENDS ComponentBase IMPLEMENTS I_Move
VAR
	_isBusy : BOOL; // underscore required if there is an IsBusy property
    state : (RETRACTED, EXTENDING, EXTENDED, RETRACTING, JAMMED_EXTENDING, JAMMED_RETRACTING);
	retractedSensor : I_Sensor;
	extendedSensor : I_Sensor;
END_VAR
```

```body
// The body of a class must not be used for code.
// Cyclic code should be called from a method if needed.
```

## Methods

Classes may use methods. Methods in a Function Block or Program should be avoided at all costs.

Methods, like classes should have one reason to exist. They should do one job and do it well.

### Naming

Always use **PascalCase** for method names. You should use verb or verb phrase for method names. Do not use prefixes.

```example
cylinder.Retract();
persistentData.Save();
```

!>Methods should not have the word 'and' in them as this is a sure sign that they are doing more than one job.

### Arguments

Methods should have as few arguments as possible. Zero arguments is best. One argument is ok. Two arguments is almost too many. Try to pass objects in to methods in order to reduce argument count. Avoid Boolean arguments as this is typically a sign that a method is dual purpose.

```example
// example of a bad method
csvReader.LoadFileAndParseResultsToArrayIfLoggingIsEnabled('file.csv',resultsArray,Logging.IsEnabled);
```

The example above has been refactored to divide the method in to smaller methods with smaller number of arguments.

```example
IF logging.IsEnabled THEN
	csvReader.LoadFile('file.csv');
	csvReader.ParseToArray(resultsArray);
END_IF
```

### Method Early Return (Guard Clauses)

Use early returns (also known as guard clauses or fail-fast logic) to avoid deep nesting and improve code readability. This style is especially useful for validating conditions up-front and exiting early when requirements are not met.

#### Early Return Example (preferred)

```example
IF NOT client.IsConnected THEN
	logger.error('Client not connected');
	RETURN;
END_IF

IF message = '' THEN
	logger.error('Invalid or missing message');
	RETURN;
END_IF

client.SendMessage(message);
logger.info('Message sent');
```

#### Nested Example (non-preferred)

This version nests the logic unnecessarily, making it harder to follow and increasing indentation. It becomes especially problematic as conditions grow in complexity.

```example
IF client.IsConnected THEN
	IF message <> '' THEN
		client.SendMessage(message);
		logger.info('Message sent');
	ELSE
		logger.error('Invalid or missing message');
	END_IF;
ELSE
	logger.error('Client not connected');
END_IF;
```

## Interfaces

The Interface Segregation Principle is a key concept in object-oriented design that encourages the creation of narrow, specific interfaces. It states that clients should not be forced to depend on interfaces they do not use.

Instead of a large, all-encompassing interface, break down functionalities into smaller, more specific interfaces. Each interface should represent a single capability or a cohesive set of functionalities.

For example, The [I_Disposable](https://disposable.mobject.org/#/i-disposable) interface has a clear, singular purpose to provide a mechanism for releasing unmanaged resources. Its sole .Dispose() method perfectly encapsulates this responsibility, ensuring that classes implementing this interface are only concerned with resource disposal. They have no access to any other non-related properties or methods.

### Naming

Always use PascalCase for interface names and add the I\_ prefix.

```example
INTERFACE I_Resettable
// implements the .Reset() method
```

### Extension

Interfaces can be combined using the keyword `EXTENDS`. You can compose more complex interfaces by extending from multiple parents.

```example
INTERFACE I_Command EXTENDS I_Resettable, I_Disposable
// implements the .Execute() method
// inherits the .Reset() and .Dispose() method
```

### Casting

Extend from Query Interface when casting is required. Initially I would do all my interfaces this way, but since then I have realized that casting simply means you have lost track of the type along the way which may point to a problem with how you handle your objects.

There are some interfaces which will use casting, such as Events. As an example an Event handler may receive an I_Event and cast it to the actual type I_SensorFailedEvent.

```example
INTERFACE I_Event EXTENDS __System.IQueryInterface
```

```example
INTERFACE I_SensorFailedEvent EXTENDS I_Event
```

## Library Version Numbering

This guide will help contributors to the mobject library understand and apply appropriate versioning schemes during different development stages using the Semantic Versioning (SemVer) system.

### Semantic Versioning (SemVer)

Semantic Versioning [(SemVer)](https://semver.org/) is a widely adopted versioning scheme that provides a consistent way to label software releases. A SemVer string is composed of three parts: MAJOR.MINOR.PATCH (e.g., v1.0.0). Each part has a specific meaning:

1. **MAJOR**: Significant changes, such as breaking changes or major feature additions, which may not be backward-compatible.
2. **MINOR**: Non-breaking updates, such as the introduction of new features or improvements, while maintaining backward compatibility.
3. **PATCH**: Bug fixes or minor updates that don't introduce new features or change existing ones, ensuring backward compatibility.

### Alpha Stage Versioning

During the alpha stage, the software is under active development, and features may be incomplete, unstable, or subject to change. Use a "0.x.y" versioning scheme for alpha releases:

- Start with "v0.1.0" for the initial alpha release.
- Increment the MINOR version when introducing new features or improvements without breaking changes (e.g., "v0.2.0").
- Increment the PATCH version when applying bug fixes or small updates that don't introduce new features (e.g., "v0.1.1").

Add a pre-release identifier "-alpha" to the version string, e.g., "v0.1.0-alpha".

### Beta Stage Versioning

In the beta stage, the software development is nearing completion, and most of the features are implemented and functional. The software may still have bugs or performance issues. Use a versioning scheme of "1.x.y" for beta releases:

- If the software is relatively stable, use "1.x.y" for the versioning.
- Increment the MINOR version when introducing new features or improvements without breaking changes (e.g., "v1.1.0").
- Increment the PATCH version when applying bug fixes or small updates that don't introduce new features (e.g., "v1.0.1").

If the software is not stable enough for a "1.x.y" version, continue using the "0.x.y" versioning scheme.

Add a pre-release identifier like "-beta" to the version string, e.g., "v1.0.0-beta".
