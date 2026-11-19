<img width="2074" height="1247" alt="image" src="https://github.com/user-attachments/assets/1ed81fdb-b5c6-44c8-8dae-5fa2d9ebd5b6" />


# Pitch Wheel

This pitch wheel is an application that allows you to click and drag along
a circular wheel to play pitches at specific ranges. The ranges are from 20Hz
to 8000Hz. This is the typically used range of human hearing.

## Why make this?

In June of 2026 I unfortunately started having an annoying ringing in my right ear.
Without giving too much information, it was starting to frustrate me that I could
not figure out the cause of it. At some point though, I saw a video where people
were commenting with the specific pitch they were hearing in their ears, and I thought,
"That is so curious, what if I made something to help with that explorative process?"

## How it works

Click at any point on the wheel, and it will fill with a coral color up until your clicking
point. The app will then calculate the pitch at this point based on the range of 20Hz to 
8000Hz. It will then play that pitch as long as you have the "Play" button pressed.

A **very large amount** of circle math was used to make the wheel work properly. It is
an SVG slider, custom made for this purpose. I did not use the native HTML input of type
slider!
