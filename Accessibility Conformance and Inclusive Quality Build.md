# Accessibility Conformance and Inclusive Quality Build

Rey Olvera
GIT 515 Advanced Web Coding
Professor Hillerman
Module 5 Accessibility Conformance and Inclusive Quality Build
September 27, 2026
ASU Sun Devils Football Gameday Hub

## Overview

This covers the accessibility work I did on the Gameday Hub for Module 5. It builds on the Module 4 media and typography work and on my Module 5 test plan. I go over what I tested, what I found, how I fixed each thing, and how I checked that the fix worked. The site is a small HTML and CSS project with no framework. All six pages load the same styles.css and share one header, one main navigation bar, and one footer. That matters because a fix in the shared markup shows up on every page at once. I tested in Chrome and Firefox on Windows with the browser tools, and I also ran a checker called axe over all six pages so the results can be repeated. I checked the pages at 1280px, 768px, and 360px wide, and at 200 percent and 400 percent zoom.


## 1. Test Scope

I tested three page types instead of all six. The six pages fall into three groups, and each group carries a different kind of risk. Since the header, footer, navigation, and skip link are the same everywhere, testing one page from each group checks the shared parts once and the unique parts where they actually live (W3C WAI Easy Checks).

The first type is the form page, FAQ and Contact (faq.html). It is the only page with a real form, and it also has the FAQ accordion, so it is where labels, required fields, focus order, and keyboard use are most likely to go wrong. The second type is the outside content and data page, Transportation and Parking (transportation.html). It is the only page with a Google Maps embed, and it also has two data tables and a plain text address. Embedded maps matter because they can be missing a title or can trap the keyboard. The third type is the everyday content page, Home (index.html). It has the main layout, the hero, the card grid, the stat list, and the navigation, so it is a good place to check headings, landmarks, and the skip link that repeat across the site. Schedule, Stadium, and Traditions reuse these same patterns, so I spot checked them instead of testing them in full (MDN Document and website structure).

## 2. Semantic Structure

Each page uses plain HTML landmarks in the order you would expect: one header, a nav labeled Primary, one main, and a footer, plus a second nav in the footer labeled Footer so the two navigation areas are easy to tell apart. Every page has one h1, and the headings step down in order without skipping a level, so the outline is clean. Each section is tied to its heading with aria-labelledby, for example quick-links-title and glance-title on Home. Links that look like buttons stay links, and the form's submit stays a real button, so how something looks never changes what it really is (MDN WAI ARIA basics).

One structure problem was in the shared header. The mobile menu is opened by a hidden checkbox, and that checkbox had aria-hidden set to true. Hiding a control that people use is a problem, because the menu toggle could not be reached or operated. The fix removes aria-hidden, gives the checkbox a clear name with aria-label of Open main menu, and wraps the hamburger symbol in an aria-hidden span so it is not read out as random text (W3C WCAG 2.1 Success Criterion 4.1.2).

A second problem was on Home. The hero banner sat between the header and main, so its heading and text were outside any landmark, which someone moving by landmark could skip past. The hero now sits inside main as the first item, so all page content is within a landmark, and the CSS keeps the full width hero flush under the header (MDN Document and website structure).

## 3. Keyboard and Focus

I set the mouse aside and moved through each page type with Tab, Shift and Tab, Enter, Space, and the arrow keys. The tab order matches what you see on screen: skip link, brand, the nav links, into main, then the footer. On FAQ the focus moves through Name, Email, Topic, and Message, then lands on Send Message, and the accordion opens and closes with Enter and Space without trapping focus. On Transportation I tabbed into the Google Maps embed and back out to make sure the keyboard is not stuck, and the map link gives a way out and a backup if the map does not load. The focus outline is set once in styles.css, and it turns white on the maroon header so it stays easy to see on the dark bar. The current page in the nav is marked with aria-current of page, which shows a gold highlight and sets the current page (MDN Keyboard accessible; W3C WAI Easy Checks).

The skip link had a bug. It pointed at the main id, but main could not take focus, so the skip link did not always move focus the way it should; in some browsers it scrolled the page but left focus at the top. Main now has tabindex of minus 1 on all six pages so it can take focus, and its outline is hidden since it is a landing spot, not a button. The skip link now moves focus into main, and the next Tab picks up from the first item of the content (W3C WCAG 2.1 Success Criterion 2.4.1).

## 4. Zoom, Reflow, Text Spacing, and Contrast

I set zoom to 200 percent and then 400 percent in a 1280px window and watched for content running off the edge or needing to scroll both ways. The layout drops to a single column, the menu button shows up, the card grids stack, and nothing is lost, which meets the reflow goal down to a 320px viewport. The two data tables are the one spot where sideways scrolling is allowed, and that scroll stays inside the table wrapper, not the whole page. Spacing and text use relative units, so extra text spacing does not cut off or overlap words (W3C WAI Easy Checks; W3C WCAG 2.1 Success Criterion 1.4.10).

For contrast I checked the color pairs that carry meaning against the WCAG limits, which are 4.5 to 1 for normal text and 3 to 1 for large text, using the exact brand colors from the CSS. Body text on white is about 17.4 to 1, maroon links on white about 8.9 to 1, the gold current page state with dark text about 11.1 to 1, gold footer links on the dark footer about 11.1 to 1, the small gray notes on white about 7.5 to 1, and the footer disclaimer gray on the dark footer about 7.5 to 1. All of these pass AA, and most also pass the stricter 7 to 1 AAA level, so I recorded the numbers as evidence instead of changing anything (W3C WCAG 2.1 Success Criterion 1.4.3).

One pair failed the contrast check. The footer brand heading is an h2, and it picked up the maroon heading color, which is only about 2 to 1 on the dark footer, well under the 4.5 to 1 minimum. This was an AA failure on all six pages. The fix sets the footer brand text to white, about 15.3 to 1 on that background, while the trident logo keeps its gold. After the fix, that pair passes (W3C WCAG 2.1 Success Criterion 1.4.3).

## 5. Forms and Tables

The contact form on faq.html gives each control one label, tied to its input with for and id, uses the right input types for name, email, topic, and message, and adds autocomplete on name and email. The required-fields note sits above the inputs with the id required-note and is linked to each required field with aria-describedby, so the rule is tied to the field. The red star stays for sighted users and is backed by real code: the required attribute, aria-required of true, and a visually hidden word required in each label, so the meaning does not rest on the star alone. The form uses novalidate with no script, so custom inline error messages are a known gap, noted in Section 10 (W3C WAI Forms Tutorial).

The tables on Schedule, Stadium, and Transportation are real data tables. Each one has a caption, column headers with scope of col, and a row header in the first column with scope of row, so each cell is tied to its row and column headings. None of them are used for layout. On small screens each table sits in a scroll wrapper, so only the table scrolls sideways while the rest of the page reflows normally (W3C WAI Tables Tutorial).

## 6. Media, Motion, and Alternatives

The site uses inline SVG icons and photos. The trident logos and the little card icons are decorative, so they carry aria-hidden of true and focusable of false, which keeps them out of the tab order. The photos carry meaning, so each one has descriptive alt text, and the responsive picture and srcset setup keeps that alt text on the image while width, height, and CSS aspect ratio hold the space so nothing jumps as images load. The four content photos have no title attribute, since a title would only repeat the alt text and show as a mouse tooltip that keyboard and touch users never see. The credit is visible in the caption (W3C WAI Images Tutorial).

The Google Maps embed on Transportation has a clear title, Map showing the location of Mountain America Stadium in Tempe, Arizona. Above it there is a full text alternative in an address element with the street address, cross streets, closest parking, and nearest light rail stop, plus a direct map link that still works if the map never loads. The links that open a new tab, the map link and the photo credits, now include a visually hidden note that says opens in a new tab, so the jump is announced instead of being a surprise. There is no audio or video, so no captions or transcripts are needed, and the only motion is a few short CSS transitions, which the prefers-reduced-motion rule in styles.css cuts down to almost nothing for people who ask for less motion (W3C WCAG 2.1 Success Criterion 2.3.3; MDN prefers-reduced-motion).

## 7. Testing Evidence

I tested in two ways. First I used the accessibility check built into the Chrome browser tools on the tested page types. Then I ran the axe checker over all six pages, which tests against the WCAG 2.0 and 2.1 A and AA rules. 

The first run reported seven problems across the six pages. Six were the same contrast failure on the footer brand heading, and one was the hero on Home sitting outside a landmark. Both are in the remediation log. After I fixed them, a second run reported no problems on all six pages. The checker also listed a few items it cannot judge on its own: the hero heading and text sit over a background image so it cannot measure their contrast, the star and the hamburger symbol are aria-hidden, an empty textarea has no text to measure, and the Google Maps embed is on another domain so it cannot look inside it. Each of these was checked in the browser, and the map frame is a real limit of the checker, not a pass. A checker covers only part of the guidelines, so the manual checks cover the rest (W3C WAI Easy Checks).

Keyboard only passes on all three page types, with extra attention on the map embed, the accordion, and the contact form. Zoom and reflow at 200 and 400 percent across the three widths. The color pairs checked by their actual values against the WCAG limits. These passes cover the things a checker misses, like the skip link focus and the new tab warnings, which are about how the page behaves rather than simple markup, plus a read-aloud pass in Firefox (W3C WAI Easy Checks).

## 8. Accessibility Tree Sample

I sampled the contact form on faq.html using the accessibility tree in the browser tools along with a read-aloud pass in Firefox. In the tree I confirmed each input shows the right role and gets its name from its label, that the required fields report required as true, and that the required-note text is linked to each required field. Read aloud, each field gave its label, its required state, and the note that starred fields are required, and the submit control read as a button named Send Message. The limit here is that this is one component, in one tool, in one browser, on Windows. It does not cover mobile tools like VoiceOver on iOS or TalkBack on Android, and it does not cover every page, so it is solid evidence for this form pattern rather than proof for the whole site (MDN WAI ARIA basics).

## 9. Remediation Log

Finding 1, Menu button hidden from the accessibility tree. Issue: the checkbox that opens the menu had aria-hidden of true. Evidence: the checker flagged it and a read-aloud pass on the header confirmed it. Impact: the menu toggle could not be reached or operated, a serious problem on every page. Priority: serious. Fix: removed aria-hidden, added aria-label of Open main menu, and wrapped the hamburger symbol in an aria-hidden span. Retest: the button now shows in the accessibility tree with a name and works by keyboard. Result: pass (W3C WCAG 2.1 SC 4.1.2).

Finding 2, Skip link did not move focus. Issue: the skip link pointed at main, but main could not take focus, so focus did not reliably land there. Evidence: keyboard only pass on all three page types. Impact: keyboard users could not reliably skip past the header, which is the whole point of the link. Priority: serious. Fix: added tabindex of minus 1 to main on all six pages and a rule to hide the outline on main. Retest: the skip link now moves focus into main and Tab continues from the content. Result: pass (W3C WCAG 2.1 SC 2.4.1).

Finding 3, Repeated tooltip on photos. Issue: four content photos had a title attribute that just repeated the alt text. Evidence: a review of the markup and a hover test. Impact: minor, since title is mouse only, so it was hidden from keyboard and touch users and doubled up the text. Priority: minor. Fix: removed the title attribute. The visible caption still shows the credit. Retest: photos read their alt text once with no repeated tooltip. Result: pass (W3C WAI Images Tutorial).

Finding 4, New tab links gave no warning. Issue: the map link and the photo credit links open a new tab with no cue. Evidence: a keyboard and read-aloud pass. Impact: moderate, since a surprise new tab can throw off keyboard users. Priority: moderate. Fix: added a visually hidden note that says opens in a new tab inside each of the six links. Retest: the new tab is now read as part of the link. Result: pass (W3C WAI Easy Checks).

Finding 5, Required note came after the fields and leaned on the star. Issue: the note about required fields sat after the inputs, and the required meaning rested mostly on a red star. Evidence: accessibility tree sample and read-aloud pass on the form. Impact: moderate, since someone could fill in a field before learning it was required. Priority: moderate. Fix: moved the note above the fields with the id required-note, linked it to each required field with aria-describedby, and added aria-required plus a visually hidden word required to each label. Retest: each required field now announces its label, its required state, and the note. Result: pass (W3C WAI Forms Tutorial).

Finding 6, Footer brand heading failed contrast. Issue: the footer brand heading is an h2 and picked up the maroon heading color, only about 2 to 1 on the dark footer, under the 4.5 to 1 AA minimum. Evidence: the checker flagged it as a contrast failure on all six pages in the first run. Impact: serious, since low vision users could not read the footer brand name, and it showed on every page. Priority: serious. Fix: made the footer brand text white, about 15.3 to 1 on the footer, while the trident logo keeps its gold. Retest: the second run shows no contrast failures. Result: pass (W3C WCAG 2.1 SC 1.4.3).

Finding 7, Hero content outside a landmark. Issue: on Home the hero sat between the header and main, so its heading and text were outside any landmark. Evidence: the checker flagged the hero in the first run. Impact: moderate, since someone moving by landmark could miss it. Priority: moderate. Fix: moved the hero inside main as the first child and adjusted the CSS so the full width hero still sits flush under the header. Retest: the second run shows no landmark problem and Home has no problems. Result: pass (MDN Document and website structure).

Finding 8, Contrast check on the rest of the pairs. Issue: confirm the other meaningful color pairs meet the limits. Evidence: ratios worked out from the CSS brand colors against the WCAG limits, then double checked by the run once the footer heading was fixed. Impact: none found, since every remaining pair passes AA and most pass AAA. Priority: minor, this is a check, not a bug. Fix: none needed. Retest: values are in Section 4 and the run is clean. Result: pass (W3C WCAG 2.1 SC 1.4.3).

## 10. Conformance Summary

### What looks solid

The landmark and heading structure, now with all content inside a landmark; one h1 per page; the labeled navigation areas and the current page marker; the focus outline; the data table headers; the reflow at 200 and 400 percent zoom; the reduced motion handling; the photo alt text and hidden decorative icons; the map title with its text backup and fallback link; and, after the footer fix, every contrast pair I measured, all meeting AA. The checker run is clean, with no problems across all six pages.

### What I fixed in this build

The menu button now has a name and shows in the accessibility tree, the skip link now moves focus into main, the repeated photo tooltips are gone, new tab links now say they open a new tab, the contact form states its required rule before the fields and backs the star with real code, the footer brand heading now meets AA contrast, and the hero on Home now sits inside main.

### What is still limited

The contact form uses novalidate with no script, so it does not yet show inline error messages or move focus to the first bad field. That is a known gap, and the next step is a small script that announces errors in a live region. The Google Maps embed is on another domain, so the checker cannot look inside it, and its controls come from Google, so they are only partly in my control; the text address and the direct map link are the backup if the map fails. The two items the checker could not judge, the hero text over an image and the empty textarea, I tested. The sample covered one component in one desktop tool and browser on Windows, so mobile tools were not tested.

### What to check again before the final release

A full recheck of all six pages after any more edits; a mobile pass with VoiceOver and TalkBack; a contrast check of the hero heading over its photo; and a retest of the contact form once the validation script is added, to confirm errors are announced and focus is handled.

## 11. AI Disclosure

I used AI to help organize the findings, suggest checks, and explain the source docs. I ran the tests, verified every fix myself, and did not claim conformance without evidence.

## References

MDN Web Docs. Document and website structure. Mozilla. https://developer.mozilla.org/en-US/docs/Learn/HTML/Introduction_to_HTML/Document_and_website_structure

MDN Web Docs. WAI ARIA basics. Mozilla. https://developer.mozilla.org/en-US/docs/Learn/Accessibility/WAI-ARIA_basics

MDN Web Docs. Keyboard accessible. Mozilla. https://developer.mozilla.org/en-US/docs/Learn/Accessibility/HTML

MDN Web Docs. prefers-reduced-motion. Mozilla. https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-motion

W3C WAI. Easy checks: A first review of web accessibility. World Wide Web Consortium. https://www.w3.org/WAI/test-evaluate/preliminary/

W3C WAI. Forms tutorial. World Wide Web Consortium. https://www.w3.org/WAI/tutorials/forms/

W3C WAI. Tables tutorial. World Wide Web Consortium. https://www.w3.org/WAI/tutorials/tables/

W3C WAI. Images tutorial. World Wide Web Consortium. https://www.w3.org/WAI/tutorials/images/

W3C. Web Content Accessibility Guidelines WCAG 2.1. World Wide Web Consortium. https://www.w3.org/TR/WCAG21/
