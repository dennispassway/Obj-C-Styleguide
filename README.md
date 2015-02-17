# My Objective-C Bible
I've started Objective-C development and like to learn from the sources that are available on the interwebs to determine the best style of coding for Objective-C. This is my Objective-C bible and it should be seen as a concatenation of multiple styleguides and tips for my own reference.

# Table of Contents
* [Styleguide](#styleguide)
** Floep
* [Plugins](#plugins)
** Flap
* [Links](#links)
** Flop

# Styleguide
This styleguide is assembled by putting together some styleguides that are available on the web.

## Properties

### Property Attributes
Property attributes should be explicitly listed, and will help new programmers when reading the code.  The order of properties should be storage then atomicity, which is consistent with automatically generated code when connecting UI elements from Interface Builder. The `*` should be **nearest** the variable, not the type.

**Example**
```objc
@property (weak, nonatomic) IBOutlet UIView *containerView;
@property (strong, nonatomic) NSString *tutorialName;
```

Properties with mutable counterparts (e.g. NSString) should prefer `copy` instead of `strong`. Even if you declared a property as `NSString` somebody might pass in an instance of an `NSMutableString` and then change it without you noticing that.  

**Example**
```objc
@property (copy, nonatomic) NSString *tutorialName;

// Wrong
@property (strong, nonatomic) NSString *tutorialName;
```

Note: IBOutlets should be `weak` unless they are a top-level item in a xib (e.g. the top-level view is `strong`, anything beneath it is `weak`).

### Naming of Properties
Properties and local variables should be camel-case with the leading word being lowercase. They should be named as descriptively as possible. Single letter variable names should be avoided except in `for()` loops.

When using properties, instance variables should always be accessed and mutated using `self.`. This means that all properties will be visually distinct, as they will all be prefaced with `self.`. An exception to this: inside initializers, the backing instance variable (i.e. _variableName) should be used directly to avoid any potential side effects of the getters/setters.

**Local variables should not contain underscores.**

**Example**
```objc
@synthesize descriptiveVariableName = _descriptiveVariableName;
```

### Dot-Notation Syntax
Dot-notation should **always** be used for accessing and mutating properties. Bracket notation is preferred in all other instances.

**Example**
```objc
view.backgroundColor = [UIColor orangeColor];
[UIApplication sharedApplication].delegate;
```

### Private Properties
Private properties should be declared in class extensions (anonymous categories) in the implementation file of a class. Named categories (such as `OBCPrivate` or `private`) should never be used unless extending another class.

**Example**
```objc
@interface OBCAdvertisement ()

@property (nonatomic, strong) GADBannerView *googleAdView;
@property (nonatomic, strong) ADBannerView *iAdView;
@property (nonatomic, strong) UIWebView *adXWebView;

@end
```

### Qualifiers
When it comes to the variable qualifiers, the qualifier (`__strong`, `__weak`, `__unsafe_unretained`, `__autoreleasing`) should be placed between the asterisks and the variable name, e.g., `NSString * __weak text`.

### Magic Numbers
Avoid magic numbers with no meaning, preferring instead a named variable or constant (see following examples).

**Good**
```objc
if ([pin length] > RBKPinSizeMax)

// Wrong
if ([pin length] < 5)
```

What is the second example checking for? We can see that the first example is checking if the pin is longer than the max allowed.

## Methods
In method signatures, there should be a space after the method type (-/+ symbol). There should be a space between the method segments (matching Apple's style).  Always include a keyword and be descriptive with the word before the argument which describes the argument. The usage of the word "and" is reserved.  It should not be used for multiple parameters as illustrated in the `initWithWidth:height:` example below.

The method signatures uses lower camel case and start with an action. Also, be descriptive: make the word before the argument describe the argument.

**Example**:
```objc
- (IBAction)showDetailViewController:(id)sender
- (id)viewWithTag:(NSInteger)tag;
- (void)setExampleText:(NSString *)text image:(UIImage *)image;
- (void)sendAction:(SEL)aSelector to:(id)anObject forAllCells:(BOOL)flag;
- (id)viewWithTag:(NSInteger)tag;
- (instancetype)initWithWidth:(CGFloat)width height:(CGFloat)height;
```

### Getters
If the method returns an attribute of the receiver, name the method after the attribute.  The use of "get" is unnecessary, unless one or more values are returned indirection.

**Example**
```objc
- (NSInteger)age
```






## Code style

### Language
US English should be used, no British!

**Example**
```objc
// Right
UIColor *myColor = [UIColor greyColor];

// Wrong
UIColor *myColour = [UIColor grayColor];
```

### Naming
Long, descriptive method and variable names are good.

**Examples**
```objc
UIButton *settingsButton;

// Wrong
UIButton *setBut;
```

```objc
static NSTimeInterval const RWTTutorialViewControllerNavigationFadeAnimationDuration = 0.3;
// Wrong
static NSTimeInterval const fadetime = 1.7;
```

### Spacing
* Indent using 4 spaces. Never indent with tabs. **Be sure to set this preference in Xcode.**
* Method braces and other braces (`if`/`else`/`switch`/`while` etc.) always open on the same line as the statement but closes on a new line.
* There should be exactly one blank line between methods to aid in visual clarity and organization.
* Whitespace within methods should separate functionality, but often there should probably be new methods.
* `@synthesize` and `@dynamic` should each be declared on new lines in the implementation.

**Example**
```objc
if (user.isHappy) {
  //Do something
}
else {
  //Do something else
}
```



## Structure












#### Golden Path
When coding with conditionals, the left hand margin of the code should be the "golden" or "happy" path.  That is, don't nest `if` statements.  Multiple return statements are OK.

**Example**
```objc
- (void)someMethod {
  if (![someOther boolValue]) {
  return;
  }

  //Do something important
}
```





## Ternary Operator
The Ternary operator, ? , should only be used when it increases clarity or code neatness. A single condition is usually all that should be used, multiple conditions is usually more understandable as an if statement, or refactored into instance variables.

**Example**
```objc
result = a > b ? x : y;
```

## Error handling
When methods return an error parameter by reference, switch on the **returned value**, not the error variable. Some of Apple’s APIs write garbage values to the error parameter (if non-NULL) in successful cases, so switching on the error can cause false negatives (and subsequently crash).

**Example**
```objc
NSError *error;
if (![self trySomethingWithError:&error]) {
    // Handle Error
}
```

## Comments
* When they are needed, comments should be used to explain **why** a particular piece of code does something. Any comments that are used must be kept up-to-date or deleted.
* Block comments should generally be avoided, as code should be as self-documenting as possible, with only the need for intermittent, few-line explanations. This does not apply to those comments used to generate documentation.

## init and dealloc
`dealloc` methods should be placed at the top of the implementation, directly after the `@synthesize` and `@dynamic` statements.
`init` should be placed directly below the `dealloc` methods of any class.

**Example of an `init` method**
```objc
- (instancetype)init {
    self = [super init]; // or call the designated initializer
    if (self) {
        // Custom initialization
    }

    return self;
}
```

## Literals
`NSString`, `NSDictionary`, `NSArray`, and `NSNumber` literals should be used whenever creating immutable instances of those objects. Pay special care that `nil` values not be passed into `NSArray` and `NSDictionary` literals, as this will cause a crash.

**Example**
```objc
NSArray *names = @[@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul"];
NSDictionary *productManagers = @{@"iPhone" : @"Kate", @"iPad" : @"Kamal", @"Mobile Web" : @"Bill"};
NSNumber *shouldUseLiterals = @YES;
NSNumber *buildingZIPCode = @10018;
```

**Not**
```objc
NSArray *names = [NSArray arrayWithObjects:@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul", nil];
```

## CGRect Functions
When accessing the `x`, `y`, `width`, or `height` of a `CGRect`, always use the `CGGeometry` instead of direct struct member access. 
From Apple's `CGGeometry` reference:

> All functions described in this reference that take CGRect data structures as inputs implicitly standardize those rectangles before calculating their results. For this reason, your applications should avoid directly reading and writing the data stored in the CGRect data structure. Instead, use the functions described here to manipulate rectangles and to retrieve their characteristics.

**Example**
```objc
CGRect frame = self.view.frame;
CGFloat x = CGRectGetMinX(frame);
CGFloat y = CGRectGetMinY(frame);
CGFloat width = CGRectGetWidth(frame);
CGFloat height = CGRectGetHeight(frame);

// Wrong
CGRect frame = self.view.frame;
CGFloat x = frame.origin.x;
CGFloat width = frame.size.width;
```

## Constants
Constants are preferred over in-line string literals or numbers, as they allow for easy reproduction of commonly used variables and can be quickly changed without the need for find and replace. Constants should be declared as `static` constants and not `#define`s unless explicitly being used as a macro.

**Example**
```objc
static NSString * const NYTAboutViewControllerCompanyName = @"The New York Times Company";

static const CGFloat NYTImageThumbnailHeight = 50.0;
```

## Image Naming
Image names should be named consistently to preserve organization and developer sanity. They should be named as one camel case string with a description of their purpose, followed by the un-prefixed name of the class or property they are customizing (if there is one), followed by a further description of color and/or placement, and finally their state.

Images that are used for a similar purpose should be grouped in respective groups in an Images folder.

**Examples**
* `RefreshBarButtonItem` / `RefreshBarButtonItem@2x` and `RefreshBarButtonItemSelected` / `RefreshBarButtonItemSelected@2x`
* `ArticleNavigationBarWhite` / `ArticleNavigationBarWhite@2x` and `ArticleNavigationBarBlackSelected` / `ArticleNavigationBarBlackSelected@2x`.

## Booleans
Since `nil` resolves to `NO` it is unnecessary to compare it in conditions. Never compare something directly to `YES`, because `YES` is defined to 1 and a `BOOL` can be up to 8 bits. This allows for more consistency across files and greater visual clarity.

**Example**
```objc
if (!someObject) {
  // Something
}

// BOOL example
if (![someObject boolValue])
```

## Xcode project
The physical files should be kept in sync with the Xcode project files in order to avoid file sprawl. Any Xcode groups created should be reflected by folders in the filesystem. Code should be grouped not only by type, but also by feature for greater clarity.



## Case Statements
Braces are not required for case statements, unless enforced by the complier.  
When a case contains more than one line, braces should be added.

There are times when the same code can be used for multiple cases, and a fall-through should be used.  A fall-through is the removal of the 'break' statement for a case thus allowing the flow of execution to pass to the next case value.  A fall-through should be commented for coding clarity.

**Example**
```objc
switch (condition) {
  case 1:
    // ** fall-through! **
  case 2:
    // code executed for values 1 and 2
    break;
  default: 
    // ...
    break;
}
```

## Class Constructor Methods
Where class constructor methods are used, these should always return type of 'instancetype' and never 'id'. This ensures the compiler correctly infers the result type. 

**Example**
```objc
@interface Airplane
+ (instancetype)airplaneWithType:(RWTAirplaneType)type;
@end
```



## Whitespace

### Imports
Separate imports from the rest of your file by 1 space. Optionally group imports if there are many (but try to have less dependencies). Generally strive to include frameworks first.

**Example**
``` obj-c
#import <AwesomeFramework/AwesomeFramework.h>
#import <AnotherFramework/AnotherFramework.h>

#import "SomeDependency.h"
#import "SomeOtherDependency.h"

@interface MyClass
```

### Interface and implementation
Use one empty line between class extension and implementation in .m file.

**Example**
``` obj-c
@interface MyClass ()

// Properties - empty line above and below

@end

@implementation MyClass

// Body - empty line above and below

@end

```

### Math Operators
When doing math use a single space between operators. Unless that operator is unary in which case don't use a space.

* When doing logic, a single space should follow the `if` and a single space should preceed the `{`
* Always end a file with a newline.
* Colon-aligning method invocation should often be avoided.  There are cases where a method signature may have more than 3 colons and colon-aligning makes the code more readable. Please do **NOT** however colon align methods containing blocks because Xcode's indenting makes it illegible.
* Whitespace should in *all* cases be used to aid readability.
* Use new lines to delimit chunks of related code (approx 4-5 lines). If more than 4-5 lines are grouped, consider refactoring those lines into another method. 
* By grouping related lines of code it naturally starts to show where the method can be refactored into smaller reusable units.
* One blank line is generally sufficient.
* Avoid extraneous new lines between nested sets of parenthesis.
* Avoid blank lines at the end of methods. (Consider delimiting the final return value with one though.)
* a new line after the opening `{` and a new line before the closing `}` are permissible. In some cases they aid readability and in others they yield an overabundance of whitespace.
* blank line after the opening `{` of the method helps give the local variables their own context
* properties that are *not* `IBOutlet`s are grouped
* `IBOutlet` properties are grouped by context

**Example of grouping**
```objc

@interface BBProofOfLossViewController () <UITableViewDataSource, UITableViewDelegate, UITextFieldDelegate, BBAutocompletePopoverDateTextFieldDelegate, BBPopoverSignatureImageViewDelegate>

@property (strong, nonatomic) NSArray *targetItems;
@property (strong, nonatomic) BBCustomForm *customForm;

@property (weak, nonatomic) IBOutlet UILabel *nameLabel;
@property (weak, nonatomic) IBOutlet UILabel *addressLabel;

@property (weak, nonatomic) IBOutlet NSLayoutConstraint *itemListHeightConstraint;

@property (weak, nonatomic) IBOutlet BBAutocompletePopoverDateTextField *formDatePopoverTextField;

@property (weak, nonatomic) IBOutlet BBPopoverSignatureImageView *witnessSignatureImageView;
@property (weak, nonatomic) IBOutlet UITextField *witnessSignatureBackgroundTextField;
@property (weak, nonatomic) IBOutlet UILabel *witnessNameLabel;

@property (weak, nonatomic) IBOutlet BBPopoverSignatureImageView *submitterSignatureImageView;
@property (weak, nonatomic) IBOutlet UITextField *submitterSignatureBackgroundTextField;
@property (weak, nonatomic) IBOutlet UILabel *submitterNameLabel;

@property (weak, nonatomic) IBOutlet BBPopoverSignatureImageView *authorizerSignatureImageView;
@property (weak, nonatomic) IBOutlet UITextField *authorizerSignatureBackgroundTextField;
@property (weak, nonatomic) IBOutlet UILabel *authorizerNameLabel;

@end
```

## Code Organization and Structure
Use `#pragma mark -`s to categorize methods in functional groupings and protocol/delegate implementations following this general structure. When using pragma marks leave 1 newline before and after.

**Example**
```objc
#pragma mark - Lifecycle

+ (instancetype)objectWithThing:(id)thing {}
- (instancetype)init {}
- (void)dealloc {}
- (void)viewDidLoad {}
- (void)didReceiveMemoryWarning {}

#pragma mark - Custom Accessors

- (void)setCustomProperty:(id)property {}
- (id)anotherCustomProperty {}

#pragma mark - Actions

- (IBAction)submitData:(id)sender {}

#pragma mark - Public

- (void)publicMethod {}

#pragma mark - Private

- (void)privateMethod {}

#pragma mark - Protocol conformance
#pragma mark - UITextFieldDelegate
#pragma mark - UITableViewDataSource
#pragma mark - UITableViewDelegate

#pragma mark - NSCopying

- (id)copyWithZone:(NSZone *)zone {}

#pragma mark - NSObject

- (NSString *)description {}
```

## Classes
Class names use upper camel case. In general there should be one class per .h/.m file.

## Prefixes
Descriptive names should generally avoid conflicts, however there are tangible benefits to using three character class name prefixes e.g. `OBCObjectSerialization`. Class name prefixes can be used to:

* filter visible files in the project navigator in Xcode
* allow you to ignore search results in files without your desired prefix
* convey the ancestry of the class (as sometimes classes have been reused between projects)
* distinguish between components of the app

Gains by prefixing:

* Shared code should be definitely be prefixed (e.g. in RoboKit).
* Class name prefixes may be avoided for CoreData entity names. 
* Avoid using overly simple names like "Model" "View" or "Object".  
* When you don't prefix and you have a namespace collision they're megahard to debug and unravel.

## Basic Code Principles
* Each function/method should aim to perform one action/task that reflects it's name.
* Since each function/method performs one action/task each one should be relatively short. If the code does not fit on one screen for example, you know you have a problem!
* Declare local variables as close to the code they are used in as possible.
* Always aim to reduce code nesting (ie a statement that is nested in an if, an if, a for and an if is hard for the brain to evaluate. Refactor!).
* You are responsible for thoroughly testing your own code before passing off to quality assurance.
* Your code must always be tested by a minimum of one other person that is not you.

# Plugins
There's a few plugins that I consider a must have while developing for iOS with Xcode. They're all available for free!

**Alcatraz**
The package manager for Xcode. With this you can install and manage all your Xcode plugins, and all the listed plugins are available on Alcatraz.
[Alcatraz](http://alcatraz.io/)

**Backlight**
Highlights the current editing line in Xcode.
[Backlight](https://github.com/limejelly/Backlight-for-XCode)

**CocoaPods**
CocoaPods integration, right into Xcode.
[CocoaPods](https://github.com/kattrali/cocoapods-xcode-plugin)

**FuzzyAutocomplete**
Enables fuzzy matching in Xcode's autocomplete, using the 'Open Quicly' algorithm.
[FuzzyAutocomplete](https://github.com/FuzzyAutocomplete/FuzzyAutocompletePlugin)

**GitDiff**
Highlights differences against git repo in Xcode source editor.
[GitDiff](https://github.com/johnno1962/GitDiff)

**KSImageNamed**
Xcode plugin that provides autocomplete for imagenamed functions.
[KSImageNamed](https://github.com/ksuther/KSImageNamed-Xcode)

**VVDocumenter-Xcode**
Xcode plugin which helps you write Javadoc style document easier.
[VVDocumenter-Xcode](https://github.com/onevcat/VVDocumenter-Xcode)

**XAlign**
An amazing plugin to align regular code. It can align anything in any way you want.
[XAlign](https://github.com/qfish/XAlign)

# Links
Here are some awesome links that are related to iOS development.

## Tips and tricks
* [Developing iOS7 Apps by Standford University](https://itunes.apple.com/us/course/developing-ios-7-apps-for/id733644550)

## Apple's official coding guidelines
* [The Objective-C Programming Language](http://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
* [Cocoa Fundamentals Guide](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CocoaFundamentals/Introduction/Introduction.html)
* [Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
* [iOS App Programming Guide](http://developer.apple.com/library/ios/#documentation/iphone/conceptual/iphoneosprogrammingguide/Introduction/Introduction.html)