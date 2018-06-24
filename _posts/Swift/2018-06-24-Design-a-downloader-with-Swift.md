---
layout: default
title:  "Design a downloader with Swift"
author: Zhirui Dai
date:   2018-06-24 18:38:58 +0200
categories: Swift
---

[Swift Category](./category.md)

To download a file, we should do these things:

- Check if a file with the same name exits in the destination.
- Replace it with the file or change the name of the new file if it exists.
- Download the file with `NSData(contentsOf:)` or with a `URLSessionDataTask`, the former one is a sync method and the later one is a async method.
- Save the file to the disk.

## Code of SwiftDownloader

**Swift Version: 4.2**

```swift
import Cocoa

class SwiftDownloader: NSObject {

    /// Check if the file exists. If it exists and replace = true, try to remove the file.
    private static func checkDestinationExistence(url: URL, replace: Bool, completion: (_ path: String, _ error: NSError?) -> Void) -> URL? {
        var destinationURL = url
        let extensionComponent = destinationURL.pathExtension
        let filename = destinationURL.deletingPathExtension().lastPathComponent
        var index = 1
        // check if the file exists
        while FileManager.default.fileExists(atPath: destinationURL.relativePath) {
            // the file exists
            if replace {    // try to delete it
                if FileManager.default.isDeletableFile(atPath: destinationURL.relativePath) {
                    // deletable
                    do {
                        try FileManager.default.removeItem(at: destinationURL)
                    } catch let error as NSError {  // error
                        completion(url.absoluteString, error)
                        return nil
                    }
                } else {
                    // undeletable, error
                    let error = NSError(domain: "NSURLErrorCannotRemoveFile", code: NSURLErrorCannotRemoveFile,
                                        userInfo: ["reason" : "An undeletable file \(destinationURL.lastPathComponent) exits"])
                    completion(url.absoluteString, error)
                    return nil
                }
                break
            } else {    // update the file name
                destinationURL.deleteLastPathComponent()
                destinationURL.appendPathComponent("\(filename)(\(index)).\(extensionComponent)")
                index = index + 1
            }
        }
        return destinationURL
    }

    /**
     Download a file with a sync method.
     - Author:
        Zhirui Dai
     - parameters:
         - origin:      The url to download the file.
         - destination: The path of the directory to save the file.
         - replace:     Whether to try to replace an existed file.
         - completion:  Invocked when error appears or the download is completed.
     */
    func downloadSync(origin url: URL, destination: URL, replace: Bool, completion: (_ path: String, _ error: NSError?) -> Void) {
        // check if the target file exists
        guard let destinationURL = SwiftDownloader.checkDestinationExistence(url: destination.appendingPathComponent(url.lastPathComponent),
                                                                             replace: replace, completion: completion)
            else { return }
        // get data from url
        guard let dataFromURL = NSData(contentsOf: url) else {
            let error = NSError(domain: "NSURLErrorResourceUnavailable", code: NSURLErrorResourceUnavailable,
                                userInfo: [ "reason" : "Fail to get data from \(url.absoluteString)"])
            completion(url.absoluteString, error)
            return
        }
        // save data to disk
        do {
            try dataFromURL.write(to: destinationURL, options: .atomic)
            completion(url.absoluteString, nil)
        } catch let error as NSError {
            completion(url.absoluteString, error)
            return
        }
    }

    /**
     Download a file with a async method.
     - Author:
     Zhirui Dai
     - parameters:
     - origin:      The url to download the file.
     - destination: The path of the directory to save the file.
     - replace:     Whether to try to replace an existed file.
     - completion:  Invocked when error appears or the download is completed.
     */
    func downloadAsync(origin url: URL, destination: URL, replace: Bool, completion:@escaping (_ path:String, _ error:NSError?) -> Void) {
        guard let destinationURL = SwiftDownloader.checkDestinationExistence(url: destination.appendingPathComponent(url.lastPathComponent),
                                                                             replace: replace, completion: completion)
            else { return }
        // make a request
        let sessionConfig = URLSessionConfiguration.default
        let session = URLSession(configuration: sessionConfig, delegate: nil, delegateQueue: nil)
        let request = NSMutableURLRequest(url: url)
        request.httpMethod = "GET"
        // make a task for this request
        let task = session.dataTask(with: request as URLRequest, completionHandler: {

            (data: Data?, response: URLResponse?, error: Error?) -> Void in

            if (error == nil) { // no error
                guard let response = response as? HTTPURLResponse else { return }
                if response.statusCode == 200 {
                    do {
                        try data?.write(to: destinationURL, options: .atomic)
                    } catch let error as NSError {
                        completion(url.absoluteString, error)
                        return
                    }
                } else {
                    let aError = NSError(domain: "NSURLErrorUnknown", code: response.statusCode,
                                         userInfo: ["reason" : "StatusCode of the response is not 200"])
                    completion(url.absoluteString, aError)
                    return
                }
            } else {    // error!
                completion(url.absoluteString, error as NSError?)
                return
            }
        })
        task.resume()
    }
}
```

## How to use it

It is very simple to use SwiftDownloader to download a file.

Here is an example:

```swift
import Foundation

let downloader = SwiftDownloader()

let origin = URL(string: "https://github.com/daizhirui/CamelStudioX_Mac/releases/download/v3.6.5/CamelStudioX_3.6.5_Build37.zip")

let destination = FileManager.default.homeDirectoryForCurrentUser.appendingPathComponent("Downloads")

// Download a file by a sync method.
downloader.downloadSync(origin: origin!, destination: destination, replace: false) {
    (path, error) in

    print(path)
    print(error?.localizedDescription ?? "")
}

// Download a file by an async method. To use this method, the main loop should keep running until it is finished.
downloader.downloadAsync(origin: origin!, destination: destination, replace: false) {
    (path, error) in

    print(path)
    print(error?.localizedDescription ?? "")
    if error == nil {
        exit(EXIT_SUCCESS)
    } else {
        exit(Int32(error?.code ?? Int(EXIT_FAILURE)))
    }
}

RunLoop.main.run()
```
