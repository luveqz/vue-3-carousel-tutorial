![Abstract figures that resembles 2 carousels][img-cover]

# Building a carousel from scratch using Vue.js

Instead of going through a complex third-party library docs, I tried to figure out how to build a "multi-card" carousel from scratch.

![Final result screenshot][img-final-result]

If you want to see a real-world example, I used the logic of this approach (inspired by a Thin Tran's [tutorial][link-tinyso]) in one of my recent projects: [sprout.fictolab.co](https://sprout.fictolab.co/).

**Bonus**: Thanks to [Matt Jenkins](https://github.com/ArtDepartmentMJ), you can now check an [updated version](https://github.com/luvejo/vue-3-carousel-tutorial/tree/composition-api) that uses the Composition API with the Setup Syntax.

_(Originally published at [DEV][link-dev])._

## 1. Understanding the structure

This is the underling structure of the demo above:

![structure][img-structure]

But let's see how it actually works:

![flow][img-flow]

Though in this .gif every step has an animated transition, this is just to make it easier to visualize all 4 steps:

1. Translate the `.inner` wrapper.
2. Extract the first item.
3. Paste it to the tail.
4. Move `.inner` back to its original position.

In the actual implementation, only step #1 will be animated. The others will happen instantly. This is what give us the impression of an infinite/continuous navigation loop. Can't you see it? Stick with me 😉

## 2. Building the carousel structure

Let's start with this basic component:

![Code sample 1](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/2z0keaykxbk2aby9jm8h.png)

This is exactly the structure from section 1. The `.carousel` container is the frame within which the cards will move.

## 3. Adding styles

![Code sample 2](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/vscq1p4nvqbq1015f0cp.png)

Explanation:

- **Line 5**: With a fixed width we are sure new items will be appended outside of the carousel's visible area. But if you have enough cards, you can make it as width as you want.
- **Line 6**: Using the property `overflow: hidden;` will allow us to crop those elements that go outside of `.carousel`.
- **Line 10**: Prevents `inline-block` elements (or `inline-flex`, in our case) from wrapping once the parent space has been filled. See [white-space][link-white-space].

Expected result:

![Result 1 screenshot][img-result-1]

## 4. Translating the `.inner` wrapper (step 1)

![Code sample 3](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/uwtve7uu649wp8zgpfrc.png)

Explanation:

- **Line 22**: The `$refs` property let you access your [template refs][link-template-refs]. `scrollWith` give us the width of an element, even if it's partially hidden due to overflow.
- **Line 24**: This will dynamically set our carousel "step", which is the distance we need to translate our `.inner` element every time the "next" or "prev" buttons are pressed. Having this, you don't even need to specify the width of your `.card` elements (as long as they're all the same size).
- **Lines 27-35**: To move the cards we'll be translating the whole `.inner` wrapper, manipulating its `transform` property.
- **Line 44**: `transform` is the property we want to animate.

Expected result:

![Result 2 screenshot][img-result-2]

## 5. Shifting the `cards[]` array (steps 2 and 3)

![Code sample 4](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/3hdum51zjxkn4fub634n.png)

Explanation:

- **Lines 3-6**: `afterTransition()` takes a callback as an argument that's going to be executed after a transition in `.inner` occurs.
- **Line 4**: The `Array.prototype.shift()` method take the first element out of the array and returns it.
- **Line 5**: The `Array.prototype.push()` method inserts an element to the end of the array.
- **Lines 10-13**: We define the event listener callback: `listener()`. It will call our actual callback and then remove itself when executed.
- **Line 14**: We add the event listener.

I encourage you to implement the `prev()` method. Hint: check this MDN [entry][link-array-methods] on Array operations.

## 6. Moving `.inner` back to its original position (step 4)

![Code sample 5](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/w9v8xudjlrudkxr09wp0.png)

Explanation:

- **Line 5**: It resets `.inner`'s position after shifting the `cards[]` array, counteracting the additional translation caused by the latter.
- **Line 13**: We set `transition` to `none` so the reset happens instantly.

Expected result:

![Result 3 screenshot][img-result-3]

## 7. Final tunings

At this point, our carousel just works. But there are a few bugs:

- **Bug 1**: Calling `next()` too often results in non-transitioned navigation. Same for `prev()`.

We need to find a way to disable those methods during the CSS transitions. We'll be using a data property `transitioning` to track this state.

![Code sample 6](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/tvkmwwjyrpq4p15r31bo.png)

- **Bug 2**: Unlike what happens with `next()`, when we call `prev()` the previous card doesn't slide-in. It just appears instantly.

If you watched carefully, our current implementation still differs from the structure proposed at the beginning of this tutorial. In the former the `.inner`'s left side and the `.carousel`'s left side aligns. In the latter the `.inner`'s left side starts outside the `.carousel`'s boundaries: the difference is the space that occupies a single card.

So let's keep our `.inner` always translated one step to the left.

![Code sample 7](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ed5qno1l5fi4viu3r32w.png)

Explanation:

- **Lines 9 and 16**: Every time we execute `moveRight()` or `moveLeft()` we are reseting all the `transform` values for `.inner`. Therefore it becomes necessary to add that additional `translateX(-${this.step})`, which is the position we want all other transformations occur from.

## 8. Conclusion

And that's it. What a trip, huh? 😅 No wonder why this is a common question in technical interviews. But now you know how to ―or another way to― build your own "multi-card" carousel.

Again, here is the [full code][link-repo]. I hope you found it useful, and feel free to share your thoughts/improvements in the comments.

Thanks for reading!

[link-tinyso]: https://medium.com/tinyso/how-to-create-the-responsive-and-swipeable-carousel-slider-component-in-react-99f433364aa0
[link-white-space]: https://developer.mozilla.org/en-US/docs/Web/CSS/white-space
[link-template-refs]: https://v3.vuejs.org/guide/component-template-refs.html
[link-array-methods]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array
[link-dev]: https://dev.to/luvejo/how-to-build-a-carousel-from-scratch-using-vue-js-4ki0
[link-repo]: https://github.com/luvejo/vue-3-carousel-tutorial
[img-flow]: https://dev-to-uploads.s3.amazonaws.com/uploads/articles/bx5ue1ym8if28u68hpjd.gif
[img-structure]: https://dev-to-uploads.s3.amazonaws.com/uploads/articles/o2baew25ru2w4bzwt045.jpg
[img-result-1]: https://dev-to-uploads.s3.amazonaws.com/uploads/articles/xn6hk3z76khe1lprexnj.png
[img-result-2]: https://dev-to-uploads.s3.amazonaws.com/uploads/articles/znvi3i7gajv03rfrz62p.gif
[img-result-3]: https://dev-to-uploads.s3.amazonaws.com/uploads/articles/a55htd9cs40ewrdjv7zj.gif
[img-final-result]: https://dev-to-uploads.s3.amazonaws.com/uploads/articles/a3ylulgdqk28liz2tj3i.gif
[img-cover]: https://res.cloudinary.com/practicaldev/image/fetch/s--1eZ5q1uw--/c_imagga_scale,f_auto,fl_progressive,h_420,q_auto,w_1000/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/rpkegg9gque7a9lg49ww.jpg
