# RxPager

[![CI Status](http://img.shields.io/travis/pgherveou/RxPager.svg?style=flat)](https://travis-ci.org/pgherveou/RxPager)
[![Version](https://img.shields.io/cocoapods/v/RxPager.svg?style=flat)](http://cocoapods.org/pods/RxPager)
[![License](https://img.shields.io/cocoapods/l/RxPager.svg?style=flat)](http://cocoapods.org/pods/RxPager)
[![Platform](https://img.shields.io/cocoapods/p/RxPager.svg?style=flat)](http://cocoapods.org/pods/RxPager)

## Usage

```swift
import RxSwift
import RxPager

typealias Page = [Int]
typealias Callback = () -> Void

// paging function, take previous Page, return Observable<Page>
let paging = { (previousPage: Page?) -> Observable<Page> in
  let last = previousPage?.last ?? 0
  return Observable.just([last + 1, last + 2, last + 3])
}

// return true if there are more pages to be emitted
let hasNext = { (page: Page) -> Bool in
  return page.last < 10 // arbitrary condition for this example
}

// create the pager
let trigger = PublishSubject<Void>()
let pager: (page: Observable<Page>, next: Callback) = (
  page: rx_pager(paging: paging, hasNext: hasNext, trigger: trigger),
  next: { _ in trigger.onNext() }
)

// or using the wrapper Pager class
// let pager: Pager<Page> = Pager(
//   paging: paging,
//   hasNext: hasNext
// )

pager
  .page
  .scan(Page()) { $0 + $1 }
  .subscribeNext { print($0) }

// print [1, 2 ,3]
pager.next() // print [1, 2 ,3, 4, 5, 6]
pager.next() // print [1, 2 ,3, 4, 5, 6, 7, 8, 9]
pager.next() // print [1, 2 ,3, 4, 5, 6, 7, 8, 9, 10, 11, 12]

```

See [Demo](https://github.com/pgherveou/RxPager/blob/master/Example/RxPager/PagerTableViewController.swift) for more examples

## Api

### `rx_pager<T>(paging:hasNext:trigger)`
global function to create a pager Observable stream

#### `paging: (Page?) -> Observable<Page>`
Take the previous page and return the next Observable<Page>

#### `hasNext: (Page?) -> Observable<Page>>`
Take the last page and return true if there are more pages to load

#### `trigger: Observable<Void>`
Trigger Observable stream used to push the next page

### `Pager<Page>(paging:hasNext)`
simple struct that wrap the page stream and the trigger

#### `pager.page: Observable<T>`
The page stream

#### `pager.next: () -> Void`
Closure used to trigger next page

## Example

To run the example project, clone the repo, and run `pod install` from the Example directory first.

## Requirements

## Installation

RxPager is available through [CocoaPods](http://cocoapods.org). To install
it, simply add the following line to your Podfile:

```ruby
pod "RxPager"
```

## Credits
This pod is inspired by inspired by @mttkay work https://gist.github.com/mttkay/24881a0ce986f6ec4b4d
and was refactored using ideas discussed here https://github.com/RxSwiftCommunity/RxSwiftExt/issues/30

## License

RxPager is available under the MIT license. See the LICENSE file for more info.
