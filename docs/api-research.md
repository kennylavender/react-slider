# API Research

Exploring different ways one might want use the slider API.

## Goals

- Allow users to override styles
- Flexiblity
  - A user may need to place dots above/below items
  - A user may need to have custom next/prev arrows
  - A user may want external components that control the slider
  
## Black Box vs White Box component API?

Not sure if this is the correct way to define these types of components, there may already be terms for these types, but I am going with this for now until I research it :)

There was a good twitter thread on this here: https://twitter.com/brad_frost/status/1090733766950223878

__Black Box component api__: components self render based on given data and options. They allow the user to provide handlers for events to extend the components behaviors. The issue with black box components is that they limit flexibilty to the provided options. The options can quickly get out of hand when attempting to match as many of your users use cases as possible. Ryan Florence has a good talk about this, [Ryan Florence - Compound Components](https://youtu.be/hEGg-3pIHlE)

__White Box component api__: building block components are provided and the user is expected to provide the connecting logic and state management. I would say [MATERIAL-UI] is an example of this, it provides the building blocks, all state management is up to the user.

Maybe we can provide a white box component api, but then also provide an easy way to create and connect state management?

## Allow users to override styles

I am not very sure of a good way to do this at the moment. [MATERIAL-UI] has pretty interesting solution and seems to be the most flexible I have seen. Using a technique like [MATERIAL-UI] though does require that a user render each component themsevles, meaning no black box Slider component.

Could use standard CSS ways of doing this:

- use specificity
  - attach class names to components that can be used to override styles.
  - could also allow a user to provide a special prefix to the class names

## API Approaches

### Provide a hook and a black box Slider component

While this approach is nice and easy for the user, it has some limitations as described above for black box component api.

#### Approach issues

- how to let the user hook into actions, `useReducer` does not allow for middleware.
  - the user could override dispatch for a middleware like effect, but I am not very fond of this idea.
- doesn't allow for easy style overrides using techniques like [MATERIAL-UI]

```js
import React from 'react'
import {
  Slider,
  userSlider,
} from '@kennylavender/react-slider'

const HomeSlider = () => {

  const options = {
    autoPlay: true,
    autoPlaySpeed: 3000,
    autoPlayPauseOnHover: true,
    displayDots: false
  }
  
  const [state, dispatch] = userSlider(options)
  
  return (
    <Slider state={state} dispatch={dispatch}>
      <p>Slide 1</p>
      <p>Slide 2</p>
      <p>Slide 3</p>
    </Slider>
  )
}
```

### Provide a hook, individual components and examples

We could provide the building block components, a hook to manage state automatically, and document examples on how to hook them up.

I think this gives us maximum flexibility and automation. I am liking the feel of this API, and I think it has room to be improved further.

```js
import React from "react";
import {
  SliderWrapper,
  SliderItems,
  SliderItem,
  SliderArrows,
  SliderPrevArrow,
  SliderNextArrow,
  SliderDots,
  SliderDot,
  useSlider,
  sliderActions,
  sliderSelectors
} from "@kennylavender/react-slider";

const HomeSlider = () => {

  const options = {
    autoPlay: true,
    autoPlaySpeed: 3000,
    autoPlayPauseOnHover: true
  }
  
  const [state, dispatch] = useSlider(options);
  
  const { currentSlideIndex } = getViewState(state)
  
  const slides = [<p>Slide 1</p>, <p>Slide 2</p>];
  
  return (
    <SliderWrapper>
      <SliderSlides>
        {slides.map(v => (
          <SliderSlide isCurrent={i === currentSlideIndex}>{v}</SliderSlide>
        ))}
      </SliderSlides>
      <SliderDots>
        {mapStateToProps(state, mapDispatchToProps(dispatch, SliderDot))}
        {slides.map((v, i) => (
          <SliderDot
            isCurrent={i === currentSlideIndex}
            onClick={() => dispatch(sliderActions.goToSlideIndex(i))}
          />
        ))}
      </SliderDots>
      <SliderArrows>
        <SliderPrevArrow onClick={() => dispatch(sliderActions.prevSlide())} />
        <SliderNextArrow onClick={() => dispatch(sliderActions.nextSlide())} />
      </SliderArrows>
    </SliderWrapper>
  );
};

```

[MATERIAL-UI]: https://material-ui.com/
