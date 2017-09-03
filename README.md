# MongoDbGenericRepository
An example of generic repository implementation using the MongoDB C# Sharp 2.0 driver (async)

Now available as a nuget package:
https://www.nuget.org/packages/MongoDbGenericRepository/

# Usage examples

This repository is meant to be inherited from. 

You are responsible for managing its lifetime, it is advised to setup this repository as a singleton.

Here is an example of repository usage, where the TestRepository is implementing 2 custom methods:

```
    public interface ITestRepository : IBaseMongoRepository
    {
        void DropTestCollection<TDocument>();
        void DropTestCollection<TDocument>(string partitionKey);
    }
    
    public class TestRepository : BaseMongoRepository, ITestRepository
    {
        public TestRepository(string connectionString, string databaseName) : base(connectionString, databaseName)
        {
        }

        public void DropTestCollection<TDocument>()
        {
            MongoDbContext.DropCollection<TDocument>();
        }

        public void DropTestCollection<TDocument>(string partitionKey)
        {
            MongoDbContext.DropCollection<TDocument>(partitionKey);
        }
    }
```

The repository can be instantiated like so:

```
ITestRepository testRepository = new TestRepository(connectionString, "MongoDbTests");
```

To add a document, its class must inherit from the `Document` class or implement the `IDocument` interface:

```
    public class MyDocument : Document
    {
        public ReadTestsDocument()
        {
            Version = 2; // you can bump the version of the document schema if you change it over time
        }
        public string SomeContent { get; set; }
    }
```

The `IDocument` interface can be seen below:

```
    /// <summary>
    /// This class represents a basic document that can be stored in MongoDb.
    /// Your document must implement this class in order for the MongoDbRepository to handle them.
    /// </summary>
    public interface IDocument
    {
        DateTime AddedAtUtc { get; set; }
        Guid Id { get; set; }
        int Version { get; set; }
    }
```
This repository also allows you to partition your document accross multiple collections, this can be useful if you are running a SaaS application and want to keep good performance.

To use partitioned collections, you must define your documents using the PartitionedDocument class, which implements the IPartitionedDocument interface:
```
    public class MyPartitionedDocument : PartitionedDocument
    {
        public CreateTestsPartitionedDocument(string myPartitionKey) : base(myPartitionKey)
        {
            Version = 1;
        }
        public string SomeContent { get; set; }
    }
```

This partitioned key will be used as a prefix to your collection name.
The collection name is derived from the name of the type of your document, is set to camel case, and is pluralized using a class taken from Humanizer (https://github.com/Humanizr/Humanizer).

```
var myDoc = new MyPartitionedDocument("myPartitionKey");
_testRepository.AddOne(myDoc);
```

The above code will generate a collection named `myPartitionKey-myPartitionedDocuments`.

Please refer to the IntegrationTests project for more usage examples.

## Copyright
Copyright © 2017

## License
mongodb-generic-repository is under MIT license - http://www.opensource.org/licenses/mit-license.php

The MIT License (MIT)

Copyright (c) 2016-2017 Alexandre Spieser

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.

==============================================================================

Inflector (https://github.com/srkirkland/Inflector)
The MIT License (MIT)
Copyright (c) 2013 Scott Kirkland

==============================================================================

Humanizer (https://github.com/Humanizr/Humanizer)
The MIT License (MIT)
Copyright (c) 2012-2014 Mehdi Khalili (http://omar.io)

==============================================================================

## Author
**Alexandre Spieser**

## Donations
Feeling like my work is worth a coffee? 
Donations are welcome and will go towards further development of this project as well as other MongoDb related projects. Use the wallet address below to donate.
BTC Donations: 1Qc5ZpNA7g66KEEMcz7MXxwNyyoRyKJJZ

*Thank you for your support and generosity!*

