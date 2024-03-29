# LambdaTool

![LambdaTool logo](logo/logo.svg)

LambdaTool is an generator used to create HackSoc emails, newsletters, and
talk adverts.

## Email Markdown Format
LambdaTool uses a slightly modified Markdown format for emails, which is then
compiled into HTML.

The only addition on top of regular Markdown is the _section_, which is denoted
by `%`. For example:

```
% Section One
This is section one's content.
Lorem ipsum...

% Section Two
This is the second section.

```

The difference between headings and sections is that sections are block-style
elements which wrap all content afterwards until the next section. This is
required to give the emails pretty markup. Here's how the ASTs of headings and
sections would look, to further show the difference:

```

Document:         # 1                      % 1
                  A                        A

                  # 2                      % 2
                  B                        B


Tree:             root                     root
                    |                        |
              -------------                -----
             /   |    |    \              /     \
           # 1   A   # 2    B           % 1     % 2
                                         |       | 
                                         A       B
```

Another much less significant change is that `hr` now has some extra styling
to make it suitable for dividing the items in the _"The Timetable"_ section of
the weekly emails.

## Talk Adverts

Talk adverts are simply generated as HTML files, allowing them to be converted
to a multitude of other formats.

If you're using `lambdatool talk-ad json`, the following format is expected:

```json
{
  "title": "What's Python really doing?",
  "speaker": "Aaron Christiansen",
  "date": "Thursday 30th January",
  "timeStart": "19:00",
  "timeEnd": "20:00",
  "location": "the Pod",
  "abstract": "Something something something...",
  "speakerImage": "A base64 encoded image"
}
```

## Usage

First, fetch all of Lambadtool's dependencies with:

```
bundle install
```

After that, there are a variety of commands you can use:

```bash
# See all available commands
bundle exec lambdatool

# Create a new email
bundle exec lambdatool email new week3

# Build a specific email to HTML
bundle exec lambdatool email build 2020-01-20-week3

# Build the latest email to HTML
bundle exec lambdatool email build latest

# Build the latest email and open it in a Firefox
bundle exec lambdatool email open latest

# Check the latest email for spelling mistakes or URLs which aren't hyperlinked
bundle exec lambdatool email lint latest

# Generate an HTML talk advert
echo "{ some json }" | bundle exec lambdatool talk-ad json
```

Created emails will be named `(intended send date)-(name).(md|html)`. Created
talk adverts will be named `(date)-(speaker).html`, with both fields converted
to lowercase and having whitespace replaced with dashes.

## Configuration

Settings can be changed in `src/config.rb`. The settings here include default
event descriptions, explicitly allowed words for the linter, and any event
name corrections which should take place automatically.

## Sending emails

**You need to send emails entirely from Firefox**. This is awful, but it's the
only way it appears to work. Run `lambdatool open latest`, Ctrl+A the page, and paste
it into a Gmail compose window. (If you do this in Chrome, you'll get a
horizontal scroll bar and some wonky sizing.)

## Things about emails

- Gmail doesn't support SVGs :(
- Most clients don't support web fonts. You can use fonts which the client
    loaded though, if you want to; for example, web Gmail is able to display
    Google Sans in emails. 
- If you need a block element for something, `table` is a _much_ safer bet
    than `div`. `div` support is really spotty in almost every single
    client. Even Gmail, which is relatively modern, acts aggressively towards
    `div`s, sometimes removing their CSS or the entire tag.
- Stick to inline styles; many clients discard `style` tags entirely.
    - Speaking of styles, Gmail doesn't appear to like single-quotes in CSS. It
        seems to throw away the _entire_ `style` attribute if it contains one.
- Even though using them seems like a good idea, base64 images have mediocre 
    support and don't work very well in Gmail. I'm hosting images on Runciman 
    instead. (I wouldn't need to if Gmail supported SVG...)
- iOS deserves extra testing, as it can sometimes decide that an email should
    be a different size and shift everything to one side, leaving a big ugly
    white gap. If you'd like to test efficiently, start a Litmus trial.