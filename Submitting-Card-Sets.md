# Simple version

At its simplest, you could [email me](mailto:ajanata@socialgamer.net) a plain text file with one card per line, using four underscores (`____`) for blanks on black cards. I would, however, ask that you put a little more effort into it to save me some time:

It would be super awesome if you can format your cards into a .sql file so I can dump them directly into the database, as my free time these days is rather sparse. This may sound complicated, but it isn't!

For black cards, this is the formatting to use:

`INSERT INTO black_cards VALUES (DEFAULT, '____ Jesus is the Jesus of ____.', 0, 2, 'VS');`

Where 0 is the "draw", 2 is the "pick", and 'VS' is what it shows over the logo as the card source (5 character max, not counting the (required) single quotes). Convention is to use four underscores (____) for blanks on cards. Nothing strictly requires this, but it keeps a consistent look. If you want an apostrophe ' in the card's text, put two of them, like so:

`INSERT INTO black_cards VALUES (DEFAULT, 'It''s the end of ____ as we know it.', 0, 1, 'VS');`

White cards:

`INSERT INTO white_cards VALUES (DEFAULT, 'Hookers and blow.', 'VS');`

Same thing with 'VS' and apostrophe doubling.

Almost all of that crap is boilerplate that is required to make the database happy. If you have any questions, just let me know.

I'll give everything a once-over before running it through the database, and can fix up minor issues quickly myself.

# Advanced version

The specific DB engine for the SQL is Postgres. Card text allows HTML, and all characters that would be required to be escaped in HTML (i.e. &amp;lt;, &amp;amp;, etc.) must be so escaped in the database. You can use any other entity you wish. Of course <br> works. I would advise against complex formatting as the column is only `text character varying(255)`.