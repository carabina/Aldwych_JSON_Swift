# Iolcos
JSON parser for Swift - for parsing and re-constituting of parsed data.

The name of this repository follows the logic of many other JSON repositories that the name should be derived from some connection to Jason, the Greek mythological hero. Iolcos was Jason's starting point in the myth of the Golden Fleece, and this JSON parser is a new starting point for the parsing of JSON in Swift.

The parser is capable of handling JSON that has been loaded as NSData and which follows an array or string : value dictionary format. (In other words standard JSON.)
#How to
The code requires the latest Xcode 6.3 Beta installed with Swift 1.2

To use simply copy the file: Resources/Parser into your app or playground. It works with iOS and OS X.

To parse NSData simply use `JSONParser.parseDictionary(json:NSData)` or `JSONParser.parseArray(json:NSData)`

In order to retrieve a value identify its type, e.g. `if let tName = jsonDictionary["trackName"]?.str {}` or `if let rCount = jsonDictionary["resultCount"]?.num {}`

Updates to results can be made like this `jsonDictionary["results"]?[0]?["trackName"] = "Something"`
#Loops

        for (k,v) in jsonDictionary {
            if let aNum = v?.num {
              println(aNum)
            }
        }

and

        for v in jsonArray {
            if let aNum = v?.num {
              println(aNum)
            }
        }


#Round-tripping data
To round-trip the JSON simply write

`NSJSONSerialization.dataWithJSONObject(jsonDictionary.dictionary, options: NSJSONWritingOptions.PrettyPrinted, error: &error)`

or

`NSJSONSerialization.dataWithJSONObject(jsonArray.array, options: NSJSONWritingOptions.PrettyPrinted, error: &error)`

#Refined Handling
For more refined handling of data, additional types can be created. If you add the iTunesData file from the Extras folder then code like this can be can be written:

    var data:NSData?
    if let url = NSURL(string:"https://itunes.apple.com/search?term=b12&limit=10"),
        d = NSData(contentsOfURL: url) {
          data = d
    }
    if let data = data,
        parsedJSON = JSONParser.parseDictionary(data),
        iTD = iTunesData(dict: parsedJSON) {
        var tracks = map(iTD.results, {x in Track(dict:x.jsonDict)})
        let m = map(tracks,{x in "Title: \(x!.trackName), Album: \(x!.collectionName)\n"})
        println(join("\n",m))
        tracks[1]?.trackName = "New Name"
        tracks[1]?.trackName
        var iT = iTD
        if let track = tracks[1] {
            iT.updateTrackDetails(track)
        }
        tracks = map(iT.results, {x in Track(dict:x.jsonDict)})
        let m2 = map(tracks,{x in "Title: \(x!.trackName), Album: \(x!.collectionName)\n"})
        println(join("\n",m2))
        iT.outputJSON()?.writeToFile("/tmp/b12.json", atomically: false)
    }
It's an example of how the data can be filtered but still renconstituted. Note: an explanation of the logic and also suitable patterns for building new types for other APIs is planned. 
#Future Functionality
At present types cannot be changed, so if a value is a String it can be only changed to another String, not a number or a dictionary, or an array. In the next update there will be a property that can be changed to enable changes of type if these are desired. There will also be choice of whether or not to allow values to become null by default this will be true but it can be turned off while still assuming that any null value can become something other than null.
#Contact
You can get in touch on twitter [@sketchyTech](http://twitter.com/sketchyTech).
