# Osmos Ads iOS Demo

A native iOS demo application that integrates the Osmos Ads SDK to fetch,
render, and track display banner advertisements.

## Objective

This project demonstrates:

- Osmos iOS SDK integration
- AU-based display ad fetching
- Dynamic banner rendering
- Multiple banner ads in a scrollable view
- 50% visibility-based impression tracking
- Click tracking and destination URL handling
- Loading, retry, and fallback states
- Error handling and event logging

---

## Requirements

- macOS
- Xcode
- iOS Simulator or physical iOS device
- Swift
- Native iOS / SwiftUI

---

## Osmos Configuration

The demo uses the following Osmos configuration:

- Client ID: `10088010`
- Product Ads Host: `demo.o-s.io`
- Display Ads Host: `demo-ba.o-s.io`

The display ad request uses:

```text
cliUbid = Any
pageType = demo_page
adUnit = banner_ads

Project Structure
OsmosAdsDemo
│
├── Ads
│   ├── Models
│   │   └── AdModel.swift
│   │
│   ├── Services
│   │   ├── AdService.swift
│   │   └── OsmosSDKAdapter.swift
│   │
│   ├── Tracking
│   │   └── ImpressionTracker.swift
│   │
│   └── Visibility
│       └── VisibilityTracker.swift
│
├── Features
│   └── BannerAds
│       ├── BannerAdsView.swift
│       ├── BannerAdsViewModel.swift
│       └── BannerAdCard.swift
│
└── README.md



How Ad Fetching Works

When the application starts:

The Osmos SDK is initialized.
The application requests display ads using the configured Ad Unit.
The response is parsed.
Banner ads are extracted from:
ads.banner_ads
The following information is used from each ad:
elements.value
elements.destination_url
impression_tracking_url
click_tracking_url
The returned ads are displayed in a vertically scrollable list.

The application can load multiple banner ads rather than displaying only
the first returned advertisement.


Impression Tracking

Impressions are triggered when at least 50% of an advertisement is visible
on screen.

A reusable VisibilityTracker calculates the visible portion of each banner.

Conceptually:

Visible Area
-------------
Total Ad Area

If the resulting visibility is greater than or equal to 0.5, the impression
is considered eligible.

Duplicate Prevention

Each ad is tracked only once.

A dedicated ImpressionTracker maintains the IDs of advertisements whose
impression event has already been fired.

This prevents repeated impression events while scrolling the same ad in and
out of the visible area.

Click Tracking

When a user taps a banner:

The Osmos click event is fired.
The destination URL from the ad response is opened.

The destination URL comes from:

elements.destination_url

Click tracking is handled separately from the UI so that SDK-specific logic
does not have to be implemented directly inside the banner view.

Loading and Error Handling

The application handles the following cases:

Loading

A loading indicator is displayed while advertisements are being fetched.

SDK Initialization Failure

If SDK initialization fails, the application displays an appropriate error
state instead of crashing.

No Ads

If the API returns no advertisements, the application displays:

Ad not available
Network / Fetch Failure

If the ad request fails, the application displays the fallback state and
provides a retry option.

Invalid or Missing Fields

Invalid advertisements or advertisements missing required information are
handled safely and do not cause the application to crash.

Retry Mechanism

The application prevents duplicate requests while an ad request is already
in progress.

After a failed request, the user can retry loading the advertisements.

The normal flow also provides a Load Ad action for manually starting an
ad request.

Event Logging

The application logs important ad lifecycle events through the application's
logging layer.

The following events are tracked:

Ad Loaded
Ad Failed
Impression Fired
Click Fired

For development and demonstration, these events can be observed in the
Xcode console.

The event log does not need to be displayed as part of the production UI.

Demo / Verification

The application should be demonstrated using the following flow:

1. Load Ads

Launch the application and use Load Ad.

Expected result:

Ad Loaded: <number> ad(s)
2. Banner Rendering

Verify that returned banner advertisements are displayed in the scrollable
container.

3. Impression Tracking

Scroll until an advertisement is at least 50% visible.

Expected result in the Xcode console:

Impression Fired

Each advertisement should fire its impression only once.

4. Click Tracking

Tap a banner advertisement.

Expected behavior:

Click Fired

The destination URL should then be opened.

5. Error / No-Ad Scenario

Use the application's no-ad/fallback demonstration or simulate a failed
response.

Expected UI:

Ad not available

The user should then be able to retry loading the advertisements.

Architecture

The application follows a simple separation-of-concerns architecture.

View

Responsible for displaying:

Loading state
Banner advertisements
Error/fallback state
Load / Retry actions
ViewModel

Responsible for:

Application state
Loading advertisements
Handling errors
Coordinating tracking
Exposing state to the SwiftUI view
AdService

Provides an abstraction for ad fetching and keeps network/SDK operations
outside the UI.

OsmosSDKAdapter

Contains Osmos SDK-specific initialization, fetching, impression, and click
operations.

VisibilityTracker

Reusable helper responsible for determining whether an advertisement has
reached the 50% visibility threshold.

ImpressionTracker

Prevents duplicate impression events for the same advertisement.

                                                
Challenges

The main implementation challenges were:

Integrating the Osmos SDK while keeping SDK-specific code isolated.
Parsing the dynamic display-ad response safely.
Detecting when 50% of a banner is visible inside a scrollable SwiftUI view.
Preventing duplicate impression events.
Coordinating click tracking with opening the destination URL.
Handling empty, invalid, or failed ad responses without crashing.
Providing a retry and fallback experience.
                                                
                                                
How to Run
                                                
Clone or download the repository.
Open the iOS project in Xcode.
Select an iOS Simulator or connected iOS device.
Build and run the application.
Tap Load Ad.
Scroll through the returned advertisements.
Verify impression and click events using the Xcode console.
