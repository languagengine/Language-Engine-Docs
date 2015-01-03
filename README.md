Language Engine
===============

Language Engine is an Objective-C SDK for adding natural language
understanding to your apps. With some minor configuration, you can turn
sentences in English into structured data that you can inspect, or even
method calls to control your software directly.

Features
--------

- Custom vocabulary (syntax and semantics)
- Automatically derive nouns and verbs
- Dispatch on input (similar to Sinatra routing but for English)

Installation
------------

Install Language Engine into your Xcode projects by adding the .framework to
the project. In your build phrases, create a copy files phase, with the
destination set to Frameworks, and drag the .framework into the list.

Import Language Engine using

    #import <LanguageEngine/LanguageEngine.h>

Contribute
----------

- Issue Tracker: https://github.com/languagengine/Language-Engine-Framework/issues
- Framework: https://github.com/languagengine/Language-Engine-Framework

Support
-------

- Docs: https://github.com/languagengine/Language-Engine-Docs

If you are having issues or questions, or want to learn more, get in touch.
We're @languagengine on Twitter, and #languagengine on IRC.

However, be aware that the framework is provided as-is, with no guarantees.

License
-------

The MIT License (MIT)

Copyright (c) 2015 Darryl McAdams

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
