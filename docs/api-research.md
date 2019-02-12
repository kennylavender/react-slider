# API Research

Exploring different ways one might want use the slider API.

## Goals

- Allow users to override styles
- Flexiblity
  - A user may need to place dots above/below items
  - A user may need to have custom next/prev arrows
  - A user may want external components that control the slider
  
## Black Box vs White Box components

Not sure if this is the correct way to define these types of components, but for now until I have time to reasearch if there is a term to define them I am going to use it.

__Black Box components__ self render based on given data and options. They allow the user to provide handlers for events to extend the components behaviors. The issue with black box components is that they limit flexibilty to the provided options. The options can quickly get out of hand when attempting to match as many of your users use cases as possible. Ryan Florence has a good talk about this, [Ryan Florence - Compound Components](https://youtu.be/hEGg-3pIHlE)

__White Box components__ provide all the building block components and expect the user to provide the connecting logic and state.

## Allow users to override styles

I am not very sure of a good way to do this at the moment. [MATERIAL-UI] has pretty interesting solution and seems to be the most flexible I have seen. Using a technique like [MATERIAL-UI] though does require that a user render each component themsevles, meaning no black box Slider component.

Could use standard CSS ways of doing this:

- use specificity
  - attach class names to components that can be used to override styles.
  - could also allow a user to provide a special prefix to the class names

## Allow a user to build their own Slider from our components

Allowing a user to build their own slider provides much more flexibility for users that need it.

```js
import {
  SliderWrapper,
  SliderItems,
  SliderItem,
  SliderArrows,
  SliderPrevArrow,
  SliderNextArrow,
  SliderDots,
  SliderDot
} from '@kennylavender/react-slider'
  

const Slider = ({ display, children }) => (
  <SliderWrapper>
    <SliderItems>
      <SliderItem />
      <SliderItem />
      <SliderItem />
      <SliderItem />
      <SliderItem />
    </SliderItems>
    <SliderArrows>
      <SliderPrevArrow />
      <SliderNextArrow />
    </SliderArrows>
    <SliderDots>
      <SliderDot onClick={} />
      <SliderDot />
      <SliderDot />
      <SliderDot />
      <SliderDot />
    </SliderDots>
  </SliderWrapper>
)

## Provide configuration and state with a HOC

Provide the user with a way to wrap a slider with

const Slider = ({ state, actions }) => {
  return (
    <SliderWrapper>
      {
        displayDots(state)
        ? (
          <SliderDots>
            {children.map((v, i) => <SliderDot onClick={actions.moveToIndex(i)} />)}
          </SliderDots>
        ) : (
          null
        )
      }
    </SliderWrapper>
  )
}

const SliderContainer = () => {
  const [state, dispatch] = useSliderState()
  return (
    <Slider state={state} dispatch={dispatch} />
  )
}
```
### Approach 4: Provide a reducer and a black box Slider component

#### Approach issues
- how to let the user hook into actions, `useReducer` does not allow for middleware.
  - the user could override dispatch for a middleware like effect, but I am not very fond of this idea.
- doesn't allow for easy style overrides using techniques like [MATERIAL-UI]

```js

import {
  Slider,
  configureSliderReducer,
} from '@kennylavender/react-slider'

const HomeSlider = () => {
  const options = {
    displayDots: false
  }
  
  const sliderReducer = configureSliderReducer(options)
  const [state, dispatch] = useReducer(sliderReducer, sliderReducer())
  
  return <Slider state={state} dispatch={dispatch} />
}
```

### Approach 5: Provide a reducer, individual components and examples

```
import {
  Slider,
  configureSliderReducer,
} from '@kennylavender/react-slider'

const HomeSlider = () => {
  const options = {
    displayDots: false
  }
  
  const sliderReducer = configureSliderReducer(options)
  const [state, dispatch] = useReducer(sliderReducer, sliderReducer())
  
  const slides = [
    <p>Slide 1</p>
    <p>Slide 2</p>
   ]
  
  return (
    <SliderWrapper>
      <SliderSlides>
        {items.map(v, => <SliderSlide>{v}</SliderSlide>}
      </SliderSlides>
      <SliderDots>
        {items.map((v,i) => <SliderDot onClick={() => dispatch(sliderActions.goToIndex(i))} />)
      </SliderDots>
    </SliderWrapper>
}
```

## Terms

### Black Box Component

A component that takes data, options and handlers and self renders children.

__Example:__ This example would render dots and arrows and attach all handlers.

```js
<Slider>
  <p>Slide 1</p>
  <p>Slide 2</p>
</Slider>
```

[MATERIAL-UI]: https://material-ui.com/
