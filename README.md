# Objective-C Styleguide

## Why?
I've started Objective-C development and like to learn from the styleguides that are avaiable on the interwebs to determine the best style of coding for Objective-C. This styleguide should be seen as a concatenation of multiple styleguides and tips to use for best practise :)

## Apple's documentation

Apple's official coding guideline links:

* [The Objective-C Programming Language](http://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
* [Cocoa Fundamentals Guide](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CocoaFundamentals/Introduction/Introduction.html)
* [Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
* [iOS App Programming Guide](http://developer.apple.com/library/ios/#documentation/iphone/conceptual/iphoneosprogrammingguide/Introduction/Introduction.html)

## Dot-Notation Syntax

Dot-notation should **always** be used for accessing and mutating properties. Bracket notation is preferred in all other instances.

**For example:**
```objc
view.backgroundColor = [UIColor orangeColor];
[UIApplication sharedApplication].delegate;
```

**Not:**
```objc
[view setBackgroundColor:[UIColor orangeColor]];
UIApplication.sharedApplication.delegate;
```

## Spacing

* Indent using 4 spaces. Never indent with tabs. Be sure to set this preference in Xcode.
* Method braces and other braces (`if`/`else`/`switch`/`while` etc.) always open on the same line as the statement but close on a new line.

**For example:**
```objc
if (user.isHappy) {
//Do something
}
else {
//Do something else
}
```
* There should be exactly one blank line between methods to aid in visual clarity and organization. Whitespace within methods should separate functionality, but often there should probably be new methods.
* `@synthesize` and `@dynamic` should each be declared on new lines in the implementation.

## Conditionals

Conditional bodies should always use braces even when a conditional body could be written without braces (e.g., it is one line only) to prevent [errors](https://github.com/NYTimes/objective-c-style-guide/issues/26#issuecomment-22074256). These errors include adding a second line and expecting it to be part of the if-statement. Another, [even more dangerous defect](http://programmers.stackexchange.com/a/16530) may happen where the line "inside" the if-statement is commented out, and the next line unwittingly becomes part of the if-statement. In addition, this style is more consistent with all other conditionals, and therefore more easily scannable.

**For example:**
```objc
if (!error) {
    return success;
}
```

**Not:**
```objc
if (!error)
    return success;
```

or

```objc
if (!error) return success;
```

### Ternary Operator

The Ternary operator, ? , should only be used when it increases clarity or code neatness. A single condition is usually all that should be evaluated. Evaluating multiple conditions is usually more understandable as an if statement, or refactored into instance variables.

**For example:**
```objc
result = a > b ? x : y;
```

**Not:**
```objc
result = a > b ? x = c > d ? c : d : y;
```

## Error handling

When methods return an error parameter by reference, switch on the returned value, not the error variable.

**For example:**
```objc
NSError *error;
if (![self trySomethingWithError:&error]) {
    // Handle Error
}
```

**Not:**
```objc
NSError *error;
[self trySomethingWithError:&error];
if (error) {
    // Handle Error
}
```

Some of Apple’s APIs write garbage values to the error parameter (if non-NULL) in successful cases, so switching on the error can cause false negatives (and subsequently crash).

## Variables

Variables should be named as descriptively as possible. Single letter variable names should be avoided except in `for()` loops.

Asterisks indicating pointers belong with the variable, e.g., `NSString *text` not `NSString* text` or `NSString * text`, except in the case of constants.

Property definitions should be used in place of naked instance variables whenever possible. Direct instance variable access should be avoided except in initializer methods (`init`, `initWithCoder:`, etc…), `dealloc` methods and within custom setters and getters. For more information on using Accessor Methods in Initializer Methods and dealloc, see [here](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmPractical.html#//apple_ref/doc/uid/TP40004447-SW6).

**For example:**

```objc
@interface NYTSection: NSObject

@property (nonatomic) NSString *headline;

@end
```

**Not:**

```objc
@interface NYTSection : NSObject {
    NSString *headline;
}
```

#### Variable Qualifiers

When it comes to the variable qualifiers [introduced with ARC](https://developer.apple.com/library/ios/releasenotes/objectivec/rn-transitioningtoarc/Introduction/Introduction.html#//apple_ref/doc/uid/TP40011226-CH1-SW4), the qualifier (`__strong`, `__weak`, `__unsafe_unretained`, `__autoreleasing`) should be placed between the asterisks and the variable name, e.g., `NSString * __weak text`.

## Prefixing

A three letter prefix (e.g. `OBC`) should always be used for class names and constants, however may be omitted for Core Data entity names. Constants should be camel-case with all words capitalized and prefixed by the related class name for clarity.

**For example:**

```objc
static const NSTimeInterval NYTArticleViewControllerNavigationFadeAnimationDuration = 0.3;
```

**Not:**

```objc
static const NSTimeInterval fadetime = 1.7;
```

Properties and local variables should be camel-case with the leading word being lowercase.

Instance variables should be camel-case with the leading word being lowercase, and should be prefixed with an underscore. This is consistent with instance variables synthesized automatically by LLVM. **If LLVM can synthesize the variable automatically, then let it.**

**For example:**

```objc
@synthesize descriptiveVariableName = _descriptiveVariableName;
```

**Not:**

```objc
id varnm;
```

## Comments

When they are needed, comments should be used to explain **why** a particular piece of code does something. Any comments that are used must be kept up-to-date or deleted.

Block comments should generally be avoided, as code should be as self-documenting as possible, with only the need for intermittent, few-line explanations. This does not apply to those comments used to generate documentation.

## init and dealloc

`dealloc` methods should be placed at the top of the implementation, directly after the `@synthesize` and `@dynamic` statements. `init` should be placed directly below the `dealloc` methods of any class.

`init` methods should be structured like this:

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

**For example:**

```objc
NSArray *names = @[@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul"];
NSDictionary *productManagers = @{@"iPhone" : @"Kate", @"iPad" : @"Kamal", @"Mobile Web" : @"Bill"};
NSNumber *shouldUseLiterals = @YES;
NSNumber *buildingZIPCode = @10018;
```

**Not:**

```objc
NSArray *names = [NSArray arrayWithObjects:@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul", nil];
NSDictionary *productManagers = [NSDictionary dictionaryWithObjectsAndKeys: @"Kate", @"iPhone", @"Kamal", @"iPad", @"Bill", @"Mobile Web", nil];
NSNumber *shouldUseLiterals = [NSNumber numberWithBool:YES];
NSNumber *buildingZIPCode = [NSNumber numberWithInteger:10018];
```

## CGRect Functions

When accessing the `x`, `y`, `width`, or `height` of a `CGRect`, always use the [`CGGeometry` functions](http://developer.apple.com/library/ios/#documentation/graphicsimaging/reference/CGGeometry/Reference/reference.html) instead of direct struct member access. From Apple's `CGGeometry` reference:

> All functions described in this reference that take CGRect data structures as inputs implicitly standardize those rectangles before calculating their results. For this reason, your applications should avoid directly reading and writing the data stored in the CGRect data structure. Instead, use the functions described here to manipulate rectangles and to retrieve their characteristics.

**For example:**

```objc
CGRect frame = self.view.frame;

CGFloat x = CGRectGetMinX(frame);
CGFloat y = CGRectGetMinY(frame);
CGFloat width = CGRectGetWidth(frame);
CGFloat height = CGRectGetHeight(frame);
```

**Not:**

```objc
CGRect frame = self.view.frame;

CGFloat x = frame.origin.x;
CGFloat y = frame.origin.y;
CGFloat width = frame.size.width;
CGFloat height = frame.size.height;
```

## Constants

Constants are preferred over in-line string literals or numbers, as they allow for easy reproduction of commonly used variables and can be quickly changed without the need for find and replace. Constants should be declared as `static` constants and not `#define`s unless explicitly being used as a macro.

**For example:**

```objc
static NSString * const NYTAboutViewControllerCompanyName = @"The New York Times Company";

static const CGFloat NYTImageThumbnailHeight = 50.0;
```

**Not:**

```objc
#define CompanyName @"The New York Times Company"

#define thumbnailHeight 2
```

## Enumerated Types

When using `enum`s, it is recommended to use the new fixed underlying type specification because it has stronger type checking and code completion. The SDK now includes a macro to facilitate and encourage use of fixed underlying types — `NS_ENUM()`

**Example:**

```objc
typedef NS_ENUM(NSInteger, NYTAdRequestState) {
    NYTAdRequestStateInactive,
    NYTAdRequestStateLoading
};
```

## Private Properties

Private properties should be declared in class extensions (anonymous categories) in the implementation file of a class. Named categories (such as `NYTPrivate` or `private`) should never be used unless extending another class.

**For example:**

```objc
@interface NYTAdvertisement ()

@property (nonatomic, strong) GADBannerView *googleAdView;
@property (nonatomic, strong) ADBannerView *iAdView;
@property (nonatomic, strong) UIWebView *adXWebView;

@end
```

## Image Naming

Image names should be named consistently to preserve organization and developer sanity. They should be named as one camel case string with a description of their purpose, followed by the un-prefixed name of the class or property they are customizing (if there is one), followed by a further description of color and/or placement, and finally their state.

**For example:**

* `RefreshBarButtonItem` / `RefreshBarButtonItem@2x` and `RefreshBarButtonItemSelected` / `RefreshBarButtonItemSelected@2x`
* `ArticleNavigationBarWhite` / `ArticleNavigationBarWhite@2x` and `ArticleNavigationBarBlackSelected` / `ArticleNavigationBarBlackSelected@2x`.

Images that are used for a similar purpose should be grouped in respective groups in an Images folder.

## Booleans

Since `nil` resolves to `NO` it is unnecessary to compare it in conditions. Never compare something directly to `YES`, because `YES` is defined to 1 and a `BOOL` can be up to 8 bits.

This allows for more consistency across files and greater visual clarity.

**For example:**

```objc
if (!someObject) {
}
```

**Not:**

```objc
if (someObject == nil) {
}
```

-----

**For a `BOOL`, here are two examples:**

```objc
if (isAwesome)
if (![someObject boolValue])
```

**Not:**

```objc
if (isAwesome == YES) // Never do this.
if ([someObject boolValue] == NO)
```

-----

If the name of a `BOOL` property is expressed as an adjective, the property can omit the “is” prefix but specifies the conventional name for the get accessor, for example:

```objc
@property (assign, getter=isEditable) BOOL editable;
```
Text and example taken from the [Cocoa Naming Guidelines](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingIvarsAndTypes.html#//apple_ref/doc/uid/20001284-BAJGIIJE).

## Singletons

Singleton objects should use a thread-safe pattern for creating their shared instance.
```objc
+ (instancetype)sharedInstance {
   static id sharedInstance = nil;

   static dispatch_once_t onceToken;
   dispatch_once(&onceToken, ^{
      sharedInstance = [[self alloc] init];
   });

   return sharedInstance;
}
```
This will prevent [possible and sometimes prolific crashes](http://cocoasamurai.blogspot.com/2011/04/singletons-your-doing-them-wrong.html).

## Imports

If there is more than one import statement, group the statements [together](http://ashfurrow.com/blog/structuring-modern-objective-c). Commenting each group is optional.

Note: For modules use the [@import](http://clang.llvm.org/docs/Modules.html#using-modules) syntax.

```objc
// Frameworks
@import QuartzCore;

// Models
#import "NYTUser.h"

// Views
#import "NYTButton.h"
#import "NYTUserView.h"
```

## Xcode project

The physical files should be kept in sync with the Xcode project files in order to avoid file sprawl. Any Xcode groups created should be reflected by folders in the filesystem. Code should be grouped not only by type, but also by feature for greater clarity.

When possible, always turn on "Treat Warnings as Errors" in the target's Build Settings and enable as many [additional warnings](http://boredzo.org/blog/archives/2009-11-07/warnings) as possible. If you need to ignore a specific warning, use [Clang's pragma feature](http://clang.llvm.org/docs/UsersManual.html#controlling-diagnostics-via-pragmas).

## Language

US English should be used.

**Preferred:**
```objc
UIColor *myColor = [UIColor whiteColor];
```

**Not Preferred:**
```objc
UIColor *myColour = [UIColor whiteColor];
```

## Naming

Apple naming conventions should be adhered to wherever possible, especially those related to [memory management rules](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html) ([NARC](http://stackoverflow.com/a/2865194/340508)).

Long, descriptive method and variable names are good.

**Preferred:**

```objc
UIButton *settingsButton;
```

**Not Preferred:**

```objc
UIButton *setBut;
```

A three letter prefix should always be used for class names and constants, however may be omitted for Core Data entity names. For any official raywenderlich.com books, starter kits, or tutorials, the prefix 'RWT' should be used.

Constants should be camel-case with all words capitalized and prefixed by the related class name for clarity.

**Preferred:**

```objc
static NSTimeInterval const RWTTutorialViewControllerNavigationFadeAnimationDuration = 0.3;
```

**Not Preferred:**

```objc
static NSTimeInterval const fadetime = 1.7;
```

Properties should be camel-case with the leading word being lowercase. Use auto-synthesis for properties rather than manual @synthesize statements unless you have good reason.

**Preferred:**

```objc
@property (strong, nonatomic) NSString *descriptiveVariableName;
```

**Not Preferred:**

```objc
id varnm;
```

### Underscores

When using properties, instance variables should always be accessed and mutated using `self.`. This means that all properties will be visually distinct, as they will all be prefaced with `self.`. 

An exception to this: inside initializers, the backing instance variable (i.e. _variableName) should be used directly to avoid any potential side effects of the getters/setters.

Local variables should not contain underscores.

## Methods

In method signatures, there should be a space after the method type (-/+ symbol). There should be a space between the method segments (matching Apple's style).  Always include a keyword and be descriptive with the word before the argument which describes the argument.

**For Example**:
```objc
- (void)setExampleText:(NSString *)text image:(UIImage *)image;
```

The usage of the word "and" is reserved.  It should not be used for multiple parameters as illustrated in the `initWithWidth:height:` example below.

**Preferred:**
```objc
- (void)setExampleText:(NSString *)text image:(UIImage *)image;
- (void)sendAction:(SEL)aSelector to:(id)anObject forAllCells:(BOOL)flag;
- (id)viewWithTag:(NSInteger)tag;
- (instancetype)initWithWidth:(CGFloat)width height:(CGFloat)height;
```

**Not Preferred:**

```objc
-(void)setT:(NSString *)text i:(UIImage *)image;
- (void)sendAction:(SEL)aSelector :(id)anObject :(BOOL)flag;
- (id)taggedView:(NSInteger)tag;
- (instancetype)initWithWidth:(CGFloat)width andHeight:(CGFloat)height;
- (instancetype)initWith:(int)width and:(int)height;  // Never do this.
```

## Property Attributes

Property attributes should be explicitly listed, and will help new programmers when reading the code.  The order of properties should be storage then atomicity, which is consistent with automatically generated code when connecting UI elements from Interface Builder.

**Preferred:**

```objc
@property (weak, nonatomic) IBOutlet UIView *containerView;
@property (strong, nonatomic) NSString *tutorialName;
```

**Not Preferred:**

```objc
@property (nonatomic, weak) IBOutlet UIView *containerView;
@property (nonatomic) NSString *tutorialName;
```

Properties with mutable counterparts (e.g. NSString) should prefer `copy` instead of `strong`. 
Why? Even if you declared a property as `NSString` somebody might pass in an instance of an `NSMutableString` and then change it without you noticing that.  

**Preferred:**

```objc
@property (copy, nonatomic) NSString *tutorialName;
```

**Not Preferred:**

```objc
@property (strong, nonatomic) NSString *tutorialName;
```

## Case Statements

Braces are not required for case statements, unless enforced by the complier.  
When a case contains more than one line, braces should be added.

```objc
switch (condition) {
  case 1:
    // ...
    break;
  case 2: {
    // ...
    // Multi-line example using braces
    break;
  }
  case 3:
    // ...
    break;
  default: 
    // ...
    break;
}

```

There are times when the same code can be used for multiple cases, and a fall-through should be used.  A fall-through is the removal of the 'break' statement for a case thus allowing the flow of execution to pass to the next case value.  A fall-through should be commented for coding clarity.

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

When using an enumerated type for a switch, 'default' is not needed.   For example:

```objc
RWTLeftMenuTopItemType menuType = RWTLeftMenuTopItemMain;

switch (menuType) {
  case RWTLeftMenuTopItemMain:
    // ...
    break;
  case RWTLeftMenuTopItemShows:
    // ...
    break;
  case RWTLeftMenuTopItemSchedule:
    // ...
    break;
}
```

## Class Constructor Methods

Where class constructor methods are used, these should always return type of 'instancetype' and never 'id'. This ensures the compiler correctly infers the result type. 

```objc
@interface Airplane
+ (instancetype)airplaneWithType:(RWTAirplaneType)type;
@end
```

More information on instancetype can be found on [NSHipster.com](http://nshipster.com/instancetype/).

## Golden Path

When coding with conditionals, the left hand margin of the code should be the "golden" or "happy" path.  That is, don't nest `if` statements.  Multiple return statements are OK.

**Preferred:**

```objc
- (void)someMethod {
  if (![someOther boolValue]) {
  return;
  }

  //Do something important
}
```

**Not Preferred:**

```objc
- (void)someMethod {
  if ([someOther boolValue]) {
    //Do something important
  }
}
```

Whitespace
==========

* Indent using 4 spaces. Never indent with tabs. Be sure to set this preference in Xcode (Xcode defaults to 4 spaces).

* Separate imports from the rest of your file by 1 space. Optionally group imports if there are many (but try to have less dependencies). Generally strive to include frameworks first.

``` obj-c
#import <AwesomeFramework/AwesomeFramework.h>
#import <AnotherFramework/AnotherFramework.h>

#import "SomeDependency.h"
#import "SomeOtherDependency.h"

@interface MyClass
```

* Use one empty line between class extension and implementation in .m file.

``` obj-c
@interface MyClass ()

// Properties - empty line above and below

@end

@implementation MyClass

// Body - empty line above and below

@end

```

* Always end a file with a newline.

![](http://cl.ly/image/2g3b0C2a3F0c/Image%202014-11-14%20at%2010.51.17%20AM.png)

* When using pragma marks leave 1 newline before and after.

``` obj-c
- (CGSize)intrinsicContentSize {
    return CGSizeMake(12, 12);
}

#pragma mark - Private

- (void)setup {
    [self addGestureRecognizer:[[NSClickGestureRecognizer alloc] initWithTarget:self action:@selector(clicked:)]];
}
```

* When doing math use a single space between operators. Unless that operator is unary in which case don't use a space.

```obj-c
NSInteger index = rand() % 50 + 25; // arc4random_uniform(50) should be used insted of `rand() % 50`, but doesn't illustrate the principle well
index++;
index += 1;
index--;
```

* When doing logic, a single space should follow the `if` and a single space should preceed the `{`

``` obj-c
if (alpha + beta <= 0) && (kappa + phi > 0) {
}
```

* Colon-aligning method invocation should often be avoided.  There are cases where a method signature may have >= 3 colons and colon-aligning makes the code more readable. Please do **NOT** however colon align methods containing blocks because Xcode's indenting makes it illegible.

Good:

```objc
// blocks are easily readable
[UIView animateWithDuration:1.0 animations:^{
    // something
} completion:^(BOOL finished) {
    // something
}];
```

Bad:

```objc
// colon-aligning makes the block indentation wacky and hard to read
[UIView animateWithDuration:1.0
                 animations:^{
                     // something
                 }
                 completion:^(BOOL finished) {
                     // something
                 }];
```

* Whitespace should in *all* cases be used to aid readability. Readability is highly subjective, so here are some rough guides:
  * Use new lines to delimit chunks of related code (approx 4-5 lines). If more than 4-5 lines are grouped, consider refactoring those lines into another method. 
    * By grouping related lines of code it naturally starts to show where the method can be refactored into smaller reusable units
  * One blank line is generally sufficient.
  * Avoid extraneous new lines between nested sets of parenthesis.
  * Avoid blank lines at the end of methods. (Consider delimiting the final return value with one though.)

Good:
  
```objc
- (void)awakeFromNib {
    UIStoryboard *signatureStoryboard = [UIStoryboard storyboardWithName:@"BBPopoverSignature" bundle:nil];
    self.signatureViewController = [signatureStoryboard instantiateViewControllerWithIdentifier:@"BBPopoverSignature"];
    self.signatureViewController.modalPresentationStyle = UIModalPresentationPopover;
    self.signatureViewController.preferredContentSize = CGSizeMake(BBPopoverSignatureWidth, BBPopoverSignatureHeight);
    self.signatureViewController.signatureImageView = self;
    
    UITapGestureRecognizer *tapRecognizer = [[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(initiateSignatureCapture)];
    [self addGestureRecognizer:tapRecognizer];
}
```
Note: 

1. all the `signatureViewController`-related lines are together
2. the new line delimits the end of configuration of `signatureViewController`
3. the `tapRecognizer` instantiation and configuration is grouped, and not mixed with unrelated code
4. a new line after the opening `{` and a new line before the closing `}` are permissible. In some cases they aid readability and in others they yield an overabundance of whitespace.

Good:
```objc
- (NSAttributedString *)aboutTermsAttributedString {

    NSDictionary *attributes = nil;
    NSError *error = nil;

    NSURL *fileURL = [NSURL fileURLWithPath:[[NSBundle mainBundle] pathForResource:@"terms_of_use" ofType:@"html"]];
    NSAttributedString *attributedString = [[NSAttributedString alloc] initWithFileURL:fileURL 
                                                                               options:@{NSDocumentTypeDocumentAttribute: NSHTMLTextDocumentType, NSCharacterEncodingDocumentAttribute: @(NSUTF8StringEncoding)}
                                                                    documentAttributes:&attributes
                                                                                 error:&error];
    if (error) {
        NSLog(@"Error: unable to load about mobile string. %@", [error localizedDescription]);
        return nil;
    }

    return attributedString;
}
```
Note:

1. blank line after the opening `{` of the method helps give the local variables their own context
2. complexity of `attributedString` initialization is more readable with colon aligning
3. final return value is immediately clear thanks to the blank line above it

Good:
```objc

@interface BBProofOfLossViewController () <UITableViewDataSource, UITableViewDelegate, UITextFieldDelegate, BBAutocompletePopoverDateTextFieldDelegate, BBPopoverSignatureImageViewDelegate>

@property (strong, nonatomic) NSArray *targetItems;
@property (strong, nonatomic) BBCustomForm *customForm;

@property (weak, nonatomic) IBOutlet UILabel *nameLabel;
@property (weak, nonatomic) IBOutlet UILabel *addressLabel;
@property (weak, nonatomic) IBOutlet UILabel *fooLabel;
@property (weak, nonatomic) IBOutlet UILabel *barLabel;
@property (weak, nonatomic) IBOutlet UILabel *instanceNumberLabel;
@property (weak, nonatomic) IBOutlet UILabel *relatedNumberLabel;
@property (weak, nonatomic) IBOutlet UILabel *bazLabel;
@property (weak, nonatomic) IBOutlet UILabel *typeOfInstanceLabel;
@property (weak, nonatomic) IBOutlet UILabel *dateLabel;

@property (weak, nonatomic) IBOutlet NSLayoutConstraint *itemListHeightConstraint;

@property (weak, nonatomic) IBOutlet BBAutocompletePopoverDateTextField *formDatePopoverTextField;

@property (weak, nonatomic) IBOutlet BBPopoverSignatureImageView *witnessSignatureImageView;
@property (weak, nonatomic) IBOutlet UITextField *witnessSignatureBackgroundTextField;
@property (weak, nonatomic) IBOutlet UILabel *witnessNameLabel;
@property (weak, nonatomic) IBOutlet UILabel *witnessLocationLabel;
@property (weak, nonatomic) IBOutlet UILabel *witnessDateLabel;

@property (weak, nonatomic) IBOutlet BBPopoverSignatureImageView *submitterSignatureImageView;
@property (weak, nonatomic) IBOutlet UITextField *submitterSignatureBackgroundTextField;
@property (weak, nonatomic) IBOutlet UILabel *submitterNameLabel;
@property (weak, nonatomic) IBOutlet UILabel *submitterLocationLabel;
@property (weak, nonatomic) IBOutlet UILabel *submitterDateLabel;

@property (weak, nonatomic) IBOutlet BBPopoverSignatureImageView *authorizerSignatureImageView;
@property (weak, nonatomic) IBOutlet UITextField *authorizerSignatureBackgroundTextField;
@property (weak, nonatomic) IBOutlet UILabel *authorizerNameLabel;
@property (weak, nonatomic) IBOutlet UILabel *authorizerLocationLabel;
@property (weak, nonatomic) IBOutlet UILabel *authorizerDateLabel;

@end

```
Note:

1. new line after the `@interface` and before the `@end`
2. properties that are *not* `IBOutlet`s are grouped
3. `IBOutlet` properties are grouped by context
4. the patterns in the grouping aid readability by allowing the eye to see inconsistencies (there are none in this case)

Bad:
```objc

- (void)viewController:(BBViewController *)viewController
        finishedWithAuth:(BBAuthentication *)auth
        error:(NSError *)error {

    //pushViewController didn't work, use ugly full screen view
    [viewController dismissViewControllerAnimated:YES completion:nil];

    if (error != nil) {

        NSLog(@"Authentication failed %@", [error description]);

    } else {

        NSString *apiURL = @"https://api.example.com/v1/quux/~?format=json";

        NSURL *url = [NSURL URLWithString:apiURL];
        NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];

        [auth authorizeRequest:request completionHandler:^(NSError *error) {

            AFHTTPRequestOperation *operation = [[AFHTTPRequestOperation alloc] initWithRequest:request];
            operation.responseSerializer = [AFJSONResponseSerializer serializer];
            [operation setCompletionBlockWithSuccess:^(AFHTTPRequestOperation *operation, id responseObject) {

                NSDictionary *responseData = (NSDictionary *)responseObject;
                RBKQuickAlert(@"Response description", responseData.description);

            } failure:^(AFHTTPRequestOperation *operation, NSError *error) {

                RBKQuickAlert(@"Response error", error.description);

            }];

            [operation start];

        }];

    }

}
```
Note: 

1. method colons aren't aligned. (Method signature is not well suited to colon aligning)
2. Happy path is nested. (Early return is missing)
3. Lots of extra blank lines that break up readability. (Whitespace is failing to define context)
4. URL string is hardcoded (not environment-aware)
5. `NSMutableURLRequest` instantiation is spread over several lines

Bad rewritten better:

```objc

// DEV_BUILD/STAGE_BUILD/PROD_BUILD are configured in Project Build Settings Preprocessor Macros
#if DEV_BUILD
static NSString * const BBAPIBaseURLString = @"https://dev.example.com/v1/quux/~?format=json"; // dev

#elif QA_BUILD 
static NSString * const BBAPIBaseURLString = @"https://qa.example.com/v1/quux/~?format=json"; // qa

#elif STAGE_BUILD
static NSString * const BBAPIBaseURLString = @"https://stage.example.com/v1/quux/~?format=json"; // stage

#elif PROD_BUILD
static NSString * const BBAPIBaseURLString = @"https://api.example.com/v1/quux/~?format=json"; // prod

#else
static NSString * const BBAPIBaseURLString = @"https://api.example.com/v1/quux/~?format=json"; // prod

#endif

- (void)viewController:(BBViewController *)viewController finishedWithAuth:(BBAuthentication *)auth error:(NSError *)error {

    //pushViewController didn't work, use ugly full screen view
    [viewController dismissViewControllerAnimated:YES completion:nil];

    if (error) {
        NSLog(@"Authentication failed %@", [error localizedDescription]);

        // TODO: call our own completion block with this error?

       return;
    }
    
    NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:[NSURL URLWithString:BBAPIBaseURLString]]; // mutable in case we need to adjust the request headers
    [auth authorizeRequest:request completionHandler:^(NSError *error) {
        
        // we should probably be checking the supplied error to decided if we need to do this operation or not
        
        AFHTTPRequestOperation *operation = [[AFHTTPRequestOperation alloc] initWithRequest:request];
        operation.responseSerializer = [AFJSONResponseSerializer serializer];
        [operation setCompletionBlockWithSuccess:^(AFHTTPRequestOperation *operation, id responseObject) {
            NSDictionary *responseData = (NSDictionary *)responseObject;
            
            // TODO: Handle our response data. Call our own completion block?
            
        } failure:^(AFHTTPRequestOperation *operation, NSError *error) {
            NSLog(@"Error: %@", error.localizedDescription);
            
            // TODO: call our own completion block when we have an error?
        }];
        
        [operation start];
    }];
}
```
Note: This method is obviously incomplete and may not, *architecturally* be optimal, however it can be still styled in a readable manner.

Organization
============

* use `#pragma mark -`s to categorize methods in functional groupings and protocol/delegate implementations following this general structure.

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

Method Signatures
=================
Use lower camel case
--------------------

Good:

```objc
answerViewController
```

Bad:

```objc
answer_view_controller
```

Start with action
-----------------
For methods that represent an action an object takes, start the name with the action.

Good:

```objc
- (IBAction)showDetailViewController:(id)sender
```

Bad:

```objc
- (void)detailButtonTapped:(id)sender
```

Getters
-------
If the method returns an attribute of the receiver, name the method after the attribute.  The use of "get" is unnecessary, unless one or more values are returned indirection.

Good:

```objc
- (NSInteger)age
```

Bad:

```objc
- (NSInteger)calcAge
- (NSInteger)getAge
```

Return Type Spacing
-------------------
For consistency method definitions should have a space between the + or - (scope) and the return type (matching Apple's style).

Good:

```objc
- (int)age
```

Bad:

```objc
-(int)age
-(int) age
```

Be Descriptive
--------------
Make the word before the argument describe the argument.

Good:

```objc
- (id)viewWithTag:(NSInteger)tag;
```

Bad:

```objc
- (id)taggedView:(NSInteger)tag;
``` 
    
**Note: this is a poor example because in general tags are an antipattern and should be avoided if at all possible**

Signature Spacing
---------------
Method parameters should not have a space between the keyword and the parameter.

Good:

```objc
- (void)setExample:(NSString *)text;
```


Bad:

```objc
- (void)setExample: (NSString *)text;
- (void)setExample:(NSString *) text;
```

Classes
=======
Class names use upper camel case, ie First word capitalized, start of new words capitalized as well.  In general there should be one class per .h/.m file.

```objc
SVSSpy
SVSWhiteSpy
SVSBlackSpy
SVSCar
SVSCarEngine
```

Namespace prefixes
==================

Descriptive names should generally avoid conflicts, however there are tangible benefits to using three character class name prefixes e.g. `RBKObjectSerialization`. Class name prefixes can be used to:

* filter visible files in the project navigator in Xcode
* allow you to ignore search results in files without your desired prefix
* convey the ancestry of the class (as sometimes classes have been reused between projects)
* distinguish between components of the app

Shared code should be definitely be prefixed (e.g. in RoboKit).

Class name prefixes may be avoided for CoreData entity names. 

Avoid using overly simple names like "Model" "View" "Object".  

When you don't prefix and you have a namespace collision they're megahard to debug and unravel.

Properties
==========
Pointer Spacing
---------------
The `*` should be **nearest** the variable, not the type.

Good:

```objc
NSString *text = @"foo";
```

Bad:

```objc
NSString * text = @"foo";
NSString* text = @"foo";
```

@property
---------

Property definitions should be used in place of ivars. When defining properties, put strong/weak/retain/assign first then nonatomic for consistency.

```objc
@property (weak, nonatomic) IBOutlet UIWebView *messageWebView;
```    

*Note: IBOutlets should be `weak` unless they are a top-level item in a xib (e.g. the top-level view is `strong`, anything beneath it is `weak`)

Info about the overhead and performance of properties vs ivars can be found [here](http://blog.bignerdranch.com/4005-should-i-use-a-property-or-an-instance-variable/).

Proper Code Nutrition
=====================
Avoid magic numbers with no meaning, preferring instead a named variable or constant (see following examples).

Integers
--------
Bad:

```objc
if ([pin length] < 5)
```

What is this checking for?

Good:

```objc
if ([pin length] > RBKPinSizeMax)
```

We can see that this is checking if the pin is longer than the max allowed.

Floats
------
In a (top level) .h you can define the float:

```objc
extern const float RBKFooFloat;
```

and then in the relevant .m file assign it a value:

```objc
const float RBKFooFloat = 18.0;
```

Strings
-------
In a (top level) .h you can define the string:

```objc
extern NSString * const RBKLocationsDatabaseName;
```

and then in the relevant .m file assign it a value:

```objc
NSString * const RBKLocationsDatabaseName = @"locations.db";
```

Note: the above is a constant pointer to an `NSString` object while the following is a pointer to a constant `NSString` object.

Bad:

```objc
const NSString * RBKConstantString = @""; // pointer to constant
// which is equivalent to
NSString const * RBKConstantString = @"";
```

Basic Code Principles
-------
* Each function/method should aim to perform one action/task that reflects it's name.
* Since each function/method performs one action/task each one should be relatively short. If the code does not fit on one screen for example, you know you have a problem!
* Declare local variables as close to the code they are used in as possible.
* Always aim to reduce code nesting (ie a statement that is nested in an if, an if, a for and an if is hard for the brain to evaluate.  Refactor!).

Testing
============
* You are responsible for thoroughly testing your own code before passing off to quality assurance.
* Your code must always be tested by a minimum of one other person that is not you.
* Automated tests should always be added to at least critical code sections at a minimum (ex. algorithm to calculate mortgage details for a bank).