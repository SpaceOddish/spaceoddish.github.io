---
layout: post
title: "Ao3 bookmarking: Appreciating authors and collecting your own stats"
date: 2026-08-17 14:30:00
tags: [ao3,reading,bookmarks,readingstats,google sheets,bookmarklet,]
---

# Bookmarking on AO3 as reading tracker

As soon as I got my AO3 account back in 2012, I immediately started making good use of the bookmark feature. Now I have 3,316 bookmarks, including works that were hidden/deleted after I bookmarked them. For those, my bookmark notes are still there, but there are no other identifying marks indicating what the work was. Sad little ghost bookmarks but I appreciate them nevertheless.

Bookmarks are by default public and a great way to appreciate the author (after kudosing and/or leaving a comment). You can make them private as well, if you want to. I remember being confused about the "add to collection" option and kept trying to add to a category of sorts like "read" or "favourite". 

They are also an awesome way to archive your reading—either all the fics you've read or just the ones that you (particularly) liked. I happen to bookmark everything I read. If something's not up my alley, I just never finish and thus never bookmark.

I have a bookmarking ritual that happens a few times a year. I open the file manager in my e-reader and sort by opening date. All the files that got the label *Finished* get a bookmark. Pocketbook allows for a favourite label in its default reader app, so favourites get that little heart icon there as well. That's how I know whether or not to add the rec option on ao3. Sometimes if I don't sit down to do my bookmarking for a few months, I don't remember what some fics were about even, so the heart icon is a good reminder. Some fics will get the special label of *absolute fave*. [In my post about 2025 reading stats](/post/reading-stats-2025/), I talked about my absolute faves last year (28 out of 179).

After the bookmarking update is completed, I fire up the [AO3 Works List bookmarklet](https://random.fangirling.net/scripts/ao3_works_stats/) on each bookmarks page (20 fics per page). I then upload them all to Google Drive and open in Google Sheets to have a look at the whole spreadsheet (sometimes things get mangled in individual cells). I copy and paste each part into a single csv and further check if everything got imported right. I used to use Excel but the csv got mangled in some way while Sheets just gets it right every time. However, there's usually something funny in the *date* column. You can even see there's something different here in the screenshot.

![Screenshot of CSV output from AO3 work list bookmarklet.](/assets/images/ao3_work_list.jpg)

# Bookmarks to reading stats pipeline

Until this year, I used to do my AO3 wrapped (reading stats) in Google Sheets. The works list from the bookmarklet csv is a ready-made list that paste into my dedicated spreadsheet and only append in the first column with a simplified *fandom general* identifier. I use it since sometimes there is more than one fandom listed by the author in a given work and I want a clear label for my sorting purposes. This is what the whole thing looks like.

![Screenshot of my AO3 stats spreadsheet.](/assets/images/ao3_stats_sheets.jpg)

The data (input) from the bookmarklet starts from row 23, everything above is referencing the rows 23 and beyond (the formulas usually have ranges up to 500-ish or more). The *fandom general* column contains the following formula:

```
=IF(ISNUMBER(SEARCH($C$2,E23)),$C$2,
IF(ISNUMBER(SEARCH($C$3,E23)),$C$3,
IF(ISNUMBER(SEARCH($C$4,E23)),$C$4,
IF(ISNUMBER(SEARCH($C$5,E23)),$C$5,
IF(ISNUMBER(SEARCH($C$6,E23)),$C$6,
IF(ISNUMBER(SEARCH($C$7,E23)),$C$7,
"nope"))))))
```

Cells C2-C7 are edited by me manually and contain the fandom keyword I want to use. E23 is a cell that contains fandoms list as downloaded with the bookmarklet in the CSV (see table below). If the word *Supernatural* appears anywhere in a cell in the *fandoms* column, the formula will return that keyword. Quite simple and eliminates the need for manual tagging, as it were. As you can see in the screenshot above, the fandom cells do contain the keyword, so the whole thing works alright everytime. If there's nothing in the *fandom general*.  column in a given row, that means either the author used a different keyword or it's another fandom I forgot to add above in C2-C7. The additional options in the nested IF formula account for situations where I read more than one fandom in a given year, checking each row from C2-C7. Last year I haven't, so they were redundant.

Then the wordcount column is particularly interesting and fun to look at, especially if you're an avid reader like me, oscillating around 200 fics a year. You can sum the wordcounts with respect to specific fandoms. I used to use the *fandom general* columns as a reference point (A23:A446), L3 is the keyword (*Supernatural* in this case), and M23:M446 is the input column with words for each fic.

```
=SUMIF($A$23:$A$446,L3,$M$23:$M$446)
```

Another fun thing to check is which author I read most frequently:

```
=ARRAY_CONSTRAIN(ARRAYFORMULA(index(D23:D575, match(max(countif(D23:D575, D23:D575)), countif(D23:D575, D23:D575), 0))), 1, 1)
```

The column it references is the D column that contains the author of each work. I got this formula from somewhere and can't really recall how exactly it works. But it does!

Then, there is the Hobbit count which divides the number of total words read in that year by the wordcount of the Hobbit (95,356). I find it easy to conceptualise how "thick" a given fic is if it were a physical book. It's also funny to thin I read 48 works that were as long as the Hobbit. 

The spreadsheet has more sheets which are not included in the screenshot. The charts I used to make relied on some pivoting of the data, easiest to do in a new sheet. Doing it like this was fun and all but quite error-prone, so I'm very happy that I learned how to wrangle all this data in R. I found some inconsistencies in my input data even so win-win overall, haha. Looking up the values from the previous year and copy-pasting them into colum A was a bit annoying (A5:A13). It's much quicker in R when you've got just a single csv for all of your (bookmarks) reading data and load it all up.

Earlier, I didn't think about it too much, updating the bookmarklet csv a few times a year but recently I noticed I got some duplicate values (likely from the fact that as you add bookmarks on AO3, entires shift from, say, page 1 to page 2 and so on). So now I would recommend a way to find and mark duplicate rows with respect to a unique value (e.g., the URL). This [tutorial](https://www.excelmojo.com/find-duplicates-in-google-sheets/) provides a good solution on how to, among others, highlight duplicates through conditional formatting. Their formula for Sheets adapted for the spreadsheet from the screenshot would be this:

```
=COUNTIF(C23:C800, C23) > 1
```

It lights up in read if a URL appears twice in the column. It's only useful if you're after unique bookmarks and not tracking re-reads—I'm not so duplicates may inflate my stats unnecessarily. I think I might post here my spreadsheet here later, maybe someone would find it useful. I'll attach a step-by-step tutorial too. Once I'm done with the R script for my 2020-2025 reading stats, I'll likely post it as well.

<script
    src="https://utteranc.es/client.js"
    repo="SpaceOddish/spaceoddish.github.io"
    issue-term="pathname"
    label="Comment"
    theme="github-light"
    crossorigin="anonymous"
    async>
    </script>
