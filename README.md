# Textarea with line numbers

A tiny Vue component to show line numbers for textarea. [Live demo](https://shenmin-z.github.io/lined-textarea/)

For source code, see ONLY `src/LinedTextarea.vue`. The rest are just [webpack vue template](https://github.com/vuejs-templates/webpack-simple). Run `npm install` and `npm run build` to test.

For example usage, checkout `src/App.vue`.

There are hard-coded style values and other known issues with it. It's just a 300-lines-less toy. So if there any meaning for this, it would be some thoughts I can share, if you want to add line numbers to textarea.

## Structure

```html
<div>
  <div>
    <p>1</p>
    <p>2</p>
    <p>3</p>
    <p>4</p>
  </di>
  <textarea></textarea>
</div>
```

### Things to consider

#### Monospace

User monospace font to have, mono-spaced font.

#### Sync height

Problem: When I resize the textarea, I want the left `div` to have the same height as the `textarea`.

How I did it: css flexbox

#### Sync scroll level

Problem: When scrolling happens on `textarea`, I want the left `div` to be scrolled as well.

How I did it: Callback on scroll event and ajust `transform: translateY()` value.

#### Adjust width of left DIV

Problem: When lines are added/removed, the width of the left `div` should be updated. For example, when you have 9 lines at first, then you add one line, now you have 10 lines. Width should be changes from 1 to 2 (characters long). (`p`s are absolute positioned)

How I did it: Adjust `padding-left` of the DIV.

#### Determine when wrap happens

Problem: There isn't wrap event I can listen to. So I have to manually calculate that. :(

How I did it:

 * Calculate how many characters one line can contain
 * Calculate how many lines one lines would spread into

 I have functions written for these: `calculateCharactersPerLine` and `getWrapTimes`. Unfortunately they are not correct 100% of the time. This is the main problem of this component.

 #### Respond to changes

 Problem: Number of characters per line can be changed.

 How I did it:

 Events that would require re-calculate:

 * Left DIV grows/shrinks
 * Scrollbar appears/disappears
 * Textarea is resized

 Update when these events occur.