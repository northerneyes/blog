---
title: Typesafe-actions
date: '2015-05-06T23:46:37.121Z'
spoiler: There’s a twist at the end.
---

I need to use it more, it is actually a very good hepler library

Espesially stuff like `ActionType`
We can use like that

```ts
type incrementAsyncAction = ReturnType<typeof incrementAsyncActionCreator>
```

But this one is more powerful version and consistent

```ts
type incrementAsyncAction = ActionType<typeof incrementAsyncActionCreator>
```

And great `StateType` which can simplify type inference from redux functions, so I don't need to construct it manually all the time. Recently I'm trying to use type inference in TS as much as possible: avoid unessesary generic etc. Sometimes it can fail, so there we can use normal generic. So forget about consistency regarding generics. The more important stuff to write less types as possible. Even in `reselect` better to use state application type inference than writing a lot of functions. The same in `compose` in recompact function but for each function in it I need to specify generics. The same for `connect` function: write generic only in `recompact` communication and avoit it if it is only one function or it doesn't matter.

```ts
export const rootReducer = combineReducers({
  counter: counterReducer,
})

export type ApplicationState = StateType<typeof rootReducer>
```

## Is it possible do not use `enum CounterActionTypes` at all

So I mean why do we need it at all? is possible to make certain `actionCreators` or `actions` and kinda inferece `constants` from them.

Because this will make our life so easier.

For example, `typesafe-actions` has `createAsyncAction` util function, so we can use `createStandartAction`, `createAsyncAction` with constants and then just to collect all actions and make constants?

So we do have `getType` and it in examples as well but `getType` works only if we use `createAction` / `createStandartAction` utils from the library

So I would say this line is much much better

```ts
yield takeLatestAction(getType(incrementAsync), incrementAsyncWorker)
```

Because I immediately understand which action triggers this worker (and btw worker is a great name as well for generators and forks related to sagas) so I can find it in the code very easy as well. Becuse right now when I work with saga I'm stuggling all the time to find based on type, an action and then find action in the UI. In this case it simplify stuff a lot.

## Action creators is first class citizen of redux

That's the whole idea. That we operate and watch only action creators and then we can extract actions from action creators and type from them as well.

The biggest problem was monitoring of using constants everywhere: so for example to create 1 action

- I needed to create 1 constants, if it is a normal js, write it twice in a constant object.
- write action itself,
- write constant in reducer
- and write it in saga.

The the pain became when I want to change something.

_So if we have only one place where we define logic about actions removing or changing something will become so much easier: complier will help me_

I cannot change some constant in one place, I need to change the source of it
