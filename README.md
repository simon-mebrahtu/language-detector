# language-detector

Language Detection Library for Java


## Language Support

### 53 Built-in Language Profiles

1. af Afrikaans
1. ar Arabic
1. bg Bulgarian
1. bn Bengali
1. cs Czech
1. da Danish
1. de German
1. el Greek
1. en English
1. es Spanish
1. et Estonian
1. fa Persian
1. fi Finnish
1. fr French
1. gu Gujarati
1. he Hebrew
1. hi Hindi
1. hr Croatian
1. hu Hungarian
1. id Indonesian
1. it Italian
1. ja Japanese
1. kn Kannada
1. ko Korean
1. lt Lithuanian
1. lv Latvian
1. mk Macedonian
1. ml Malayalam
1. mr Marathi
1. ne Nepali
1. nl Dutch
1. no Norwegian
1. pa Punjabi
1. pl Polish
1. pt Portuguese
1. ro Romanian
1. ru Russian
1. sk Slovak
1. sl Slovene
1. so Somali
1. sq Albanian
1. sv Swedish
1. sw Swahili
1. ta Tamil
1. te Telugu
1. th Thai
1. tl Tagalog
1. tr Turkish
1. uk Ukrainian
1. ur Urdu
1. vi Vietnamese
1. zh-cn Simplified Chinese
1. zh-tw Traditional Chinese

### Other Languages

You can create a language profile for your own language easily. Or provide us "training text", see "How You Can Help".


## How it Works

The software uses language profiles which were created based on common text for each language.
N-grams http://en.wikipedia.org/wiki/N-gram were then extracted from that text, and that's what is stored in the profiles.

When trying to figure out in what language a certain text is written, the program goes through the same process:
It creates the same kind of n-grams of the input text. Then it compares the relative frequency of them, and finds the
language that matches best.


### Challenges

This software does not work as well when the input text to analyze is short, or unclean. For example tweets.

When a text is written in multiple languages, the default algorithm of this software is not appropriate.
You can try to split the text (by sentence or paragraph) and detect the individual parts. Running the language guesser
on the whole text will just tell you the language that is most dominant, in the best case.

This software cannot handle it well when the input text is in none of the expected (and supported) languages.
For example if you only load the language profiles from English and German, but the text is written in French,
the program may pick the more likely one, or say it doesn't know. (An improvement would be to clearly detect that
it's unlikely one of the supported languages.)


## How to Use

#### Language Detection for your Text

    //load all languages:
    List<LanguageProfile> languageProfiles = new LanguageProfileReader().readAll();

    //build language detector:
    LanguageDetector languageDetector = LanguageDetectorBuilder.create(NgramExtractors.standard())
            .withProfiles(languageProfiles)
            .build();

    //create a text object factory
    TextObjectFactory textObjectFactory = CommonTextObjectFactories.forDetectingOnLargeText();

    //query:
    TextObject textObject = textObjectFactory.forText("my text");
    Optional<String> lang = languageDetector.detect(textObject);


#### Creating Language Profiles for your Training Text

    //create text object factory:
    TextObjectFactory textObjectFactory = CommonTextObjectFactories.forIndexingCleanText();

    //load your training text:
    TextObject inputText = textObjectFactory.create()
            .append("this is my")
            .append("training text")

    //create the profile:
    LanguageProfile languageProfile = new LanguageProfileBuilder("en")
            .ngramExtractor(NgramExtractors.standard())
            .addText(inputText)
            .build();

    //store it to disk if you like:
    new LanguageProfileWriter().writeToDirectory(languageProfile, "c:/foo/bar");



## How You Can Help

If your language is not supported yet, then you can provide clean "training text", that is, common text written in your
language. The text should be fairly long (a couple of pages at the very least). If you can provide that, please open
a ticket.

If your language is supported already, but not identified clearly all the time, you can still provide such training
text. We might then be able to improve detection for your language.

If you're a programmer, dig in the source and see what you can improve. Check the open tasks.


## History and Changes

This is a fork from https://code.google.com/p/lang-guess/ (forked on 2014-02-27) which itself is a fork
of the original project https://code.google.com/p/language-detection/

#### Changes made here

##### Functional Changes

* Made results for short text consistent, no random n-gram selection for short text (configurable).
* Configurable when to remove ASCII (foreign script). Old code did it when ascii was less than 1/3 of the content, and only for ASCII.
* New n-gram generation. Faster, and flexible (filter, space-padding). Previously it was hardcoded to 1, 2 and 3-grams, and it has hardcoded which n-grams were ignored.
* LanguageDetector is now safe to use multi-threaded.
* Clear code to safely load profiles and use them, no state in static fields.
* Easier to generate your own language profiles based on training text, and to load and store them.
* Feature to weight prefix and suffix n-grams higher.

##### Technical Changes

* Updated to use Java 7 for compilation, and for syntax. It's 2014, and 7 is the only officially supported version by Oracle.
* Code quality improvements:
  * Returning interfaces instead of implementations (List instead of ArrayList etc)
  * String .equals instead of ==
  * Replaced StringBuffer with StringBuilder
  * Renamed classes for clarity
  * Made classes immutable, and thus thread safe
  * Made fields private, using accessors
  * Clear null reference concept:
    * using IntelliJ's @Nullable and @NotNull annotations
    * using Guava's Optional
  * Added JavaDoc, fixed typos
  * Added interfaces
  * More tests. Thanks to the refactorings, code is now testable that was too much embedded before.
* Removed the "seed" completely (for the Random() number generator, I don't see the use).
* Updated all Maven dependency versions
* Replaced last lib dependency with Maven (jsonic)

##### Legal

Changed license from Apache2 to LGPLv3.
Still pretty much the same, business-friendly, but requires that derivative works are published publicly also.


##### TODO

* Using a logger instead of System.out.println
* Remove old code (still a bit left)


#### Why so much forking?

The original project hasn't seen any commit in a while. The issue list is growing.
The news page says for 2012 that it now has Maven support, but there is no pom in git.
There is a release in Maven see http://mvnrepository.com/artifact/com.cybozu.labs/langdetect/1.1-20120112
for version 1.1-20120112 but not in git. So I don't know what's going on there.

The lang-guess fork saw quite some commits in 2011 and up to march 2012, then nothing anymore.
It uses Maven.

The 2 projects are not in sync, it looks like they did not integrate changes from each other anymore.

Both are on Google Code, I believe that GitHub is a much better place for contributing.

My goals were to bring the code up to current standards, and to update it for Java 7. So I quickly
noticed that I have to touch pretty much all code. And with the status of the other two projects,
I figured that I better start my own. This ensures that my work is published to the public.



## License

LGPLv3 (business friendly, must publish changes)



## Authors

###### Nakatani Shuyo

* Started the project and built most of the functionality. Provided the language profiles.
* Project is at https://code.google.com/p/language-detection/

###### Francois ROLAND

* Forked to https://code.google.com/p/lang-guess/
* Maven integration

###### Fabian Kessler

* Forked to https://github.com/optimaize/language-detector from Francois on 2014-02-27
* Rewrote most of the code
* Added JavaDoc
* See changes above, or check the GitHub commit history


