example-sentiment
=================

> This article originally appeared on [Nodejitsu](http://blog.nodejitsu.com/npmawesome-sentiment/) and [npm-awesome](http://npmawesome.com/posts/2014-04-30-sentiment/).

Sentiment analysis is one of those things you don't get to do very often, as cool as it sounds. It's one of those things I always wanted to do but haven't had a reason to yet. The field of natural language is as deep and complex as you want it to be. [Stanford Sentiment Analysis](http://nlp.stanford.edu/sentiment/) is a pretty massive project and nets pretty decent results.

[sentiment] module by [Andrew Sliwinski](https://github.com/thisandagain) takes a very simple approach to the problem of just counting token words and their preassigned weight. For the most part you can expect pretty average results and it gets completely lost with expressions like "I don't know half of you half as well as I should like, and I like less than half of you half as well as you deserve" (frankly I don't understand what it says either). But the cool thing is - it actually works and you don't need a giant database. [sentiment] can get you started down the path until you feel the need for something more involved.

    npm install sentiment

## Features

[sentiment] uses the [AFINN-111](http://www2.imm.dtu.dk/pubdb/views/publication_details.php?id=6010) wordlist to perform sentiment analysis on arbitrary blocks of input text and provides serveral things:

* Performance
* The ability to append and overwrite word / value pairs from the AFINN wordlist
* A build process that makes updating sentiment to future versions of the AFINN word list trivial

## Usage

Here's a simple example analysing bunch of latest [DHH tweets](https://www.twitter.com/dhh).

    var
      request = require('request'),
      cheerio = require('cheerio'),
      sentiment = require('sentiment')
      ;

    function sentimentToSmiley(sentiment) {
      var score = sentiment.score;

      if(score === 0) { return ':-|' }
      if(score < 0) {
        if(score > -2) { return ':-(' }
        return ':`('
      }

      if(score < 2) { return ':-)' }
      return ':-D'
    }

    request('https://www.twitter.com/dhh', function(err, response) {
      var $ = cheerio.load(response.body);

      $('.ProfileTweet-text').toArray().forEach(function(item) {
        var text = $(item).text();
        var results = sentiment(text);
        console.log(sentimentToSmiley(results), '-', text.replace(/\n/g, ' '));
      });
    });

Checkout out [functional example on runnable](http://runnable.com/U3FuXTY9W-JoY5vl). Running this will produce result similiar to:

    :-| - @mrbigdreamerYWF We tried to get NBC but they wouldn't cover it :)
    :-| - @AmianExclusive I think the C7R is the most expensive customer GTE car you can buy. And still not a proven thing.
    :-| - @AmianExclusive Corvette factory team will be at Le Mans but not Viper :(. Apparently they didn't sell enough cars to afford it.
    :-| - Goooood morning #RailsConf! My talk is at 9:15am central. It will be streamed live on justin.tv/confreaks, if you're not in Chicago.
    :-| - @ashesfall Hot diggity.
    :-) - (Yes, internet is fast elsewhere too, but most services of note have their app servers in the US, so that's the ping that counts).
    :-| - @arunagw Just about everything on the internet of note, which happens to have app servers in the US :D
    :-| - For those who can't attend RailsConf, we'll be livestreaming the keynotes here: justin.tv/confreaks Schedule here: railsconf.com/schedule
    :-D - It gets me every time I'm back in the US - even if just out for a few weeks: 20ms pings is internet happiness. So fast.
    :-| - @turbineseaplane Nope. May depend on hand size? Could see it be harder to hold with small hands.
    :-| - @mhauer I find that the hybrid model is actually the most common in anything but the newest or smallest companies. Very workable.
    :-( - @N8Gard @railsconf They're going to record it, I think. So no worries.
    :-( - @dennismajor1 @thereaIbanksy I'd prefer to look at it the other way: drop the religious nonsense and enjoy a party without sin!
    :-| - @turbineseaplane It is sometimes a little awkward but the sacrifice is worth it.
    :-| - @andrestuul Right on the last one, not on the first two. Connecting has never been easier. 10 yrs ago I was working from Denmark.
    :-| - @davegreiner @dhh we had thousands of accounts on Cork’d when it was still on a Mac mini in my friend’s rack (Rails 1.x)
    :`( - @GregMolnar Horrible contortions to the false idol of testing. More on this in my keynote at RailsConf tomorrow.
    :-D - @dhh Sounds familiar. CM was hosted on a cheap box with all our client's sites. Went dedicated when it started slowing them down 1 yr later.
    :`( - @thomasfuchs @letsfreckle Fuck yeah! But you did have to forgo such necessities as a big fancy office on day 1!
    :-) - @homakov @davidpetschull I was in Denmark while I built Basecamp. But yes timezone and language can be barriers.

## Closing thoughts

[sentiment] is a very simple token counter which has vocabulary of about 2.5k english words. It doesn't always produce reliable results, but it gets you a step forward if you need to do sentiment analysis without bringing in the heavy weight APIs or expensive commercial libraries.

I can totally imagine processing customer inquiries coming in via email, twitter and other channels with [sentiment]. Higher priority could be assigned to inquires with lower, more negative scores.

[sentiment]: https://github.com/thisandagain/sentiment
[Alex Gorbatchev]: https://github.com/alexgorbatchev
[npmawesome.com]: http://npmawesome.com
[npm-awesome-site]: https://github.com/alexgorbatchev/npm-awesome-site

