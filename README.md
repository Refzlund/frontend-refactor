# svelte-awesome-pug
Using the [Svelte Preprocessor to process Pug](https://github.com/sveltejs/svelte-preprocess/blob/main/docs/preprocessing.md#pug)  can look a bit funky.

svelte-awesome-pug unfunkies it in a good way.

## Install

Wrap the svelte preprocessor with the awesome-pug pre and post processors 

```ts
import { awesomePugPre, awesomePugPost } from 'svelte-awesome-pug'

/** @type {import('@sveltejs/kit').Config} */
const config = {
    preprocess: [
        awesomePugPre,
        preprocess(),
        awesomePugPost
    ],
```

## Incompatibity
I'm a [TAB] kind of guy, so I haven't supported (or thought of) space indentation yet. The code is pretty simple so feel free to make a pull-request!

## Key features

Is compatible with old-style pug. So any existing pug code shouldn't break.

### No need for != and quotes
```pug
    //- Before
    .some-div(on:click!='{() => ...}')
```
```pug
    //- After
    .some-div(on:click={() => ...})
```

### Indented components
```pug
    Input.Text()
    // Results in <Input class="Text">
```
```pug
    Input.Text()
    // Results in <Input.Text>
```

### Spreading objects
```pug
    .some-div('{...$$restProps}')
    //         ^ ts(-1) error
```
```pug
    .some-div(...$$restProps)
    // Results in <div class="some-div" {...$$restProps}>
```

## More
Okay, so making pug not look like 💩 is great. Now ... here are some personal additions of functionality (like them or not)

### `export:`
Export will export/(forward) the attribute for you. Like on:click forwards events, export:class will allow the component to retrieve the `class` attribute:

```pug
    //- Component.svelte
    .some-div(export:class)
```
```pug
    Component.another-class
```

The attribute input will be default value. (With an exception of `export:class` and `export:style` which will always have the default value of `''` if you don't provide any)

So..
```pug
    //- Component.svelte
    Table(export:data)
    //- Becomes    let __export_data__

    ...
    
    Component
    //- Component was created without the export `data`
    
    


    //- Component.svelte
    Table(export:data={undefined})
    //- Becomes    let __export_data__ = undefined

    ...
    
    Component
```

### `style:` and `class:` directives
You can't normally do
```pug
    Component(class:active class:somebool)
    //- "Can't use class directive on components"
```

However, with ***svelte-awesome-pug*** we can add a space in-between AND have multiple like like so:
```pug
    Component(class :active :somebool)
    //- Becomes   <Component class="{active ? 'active' : ''}  {somebool ? 'somebool' : ''}">

    Component(class :enabled={isEnabled})
    //- Becomes   <Component class="{isEnabled ? 'enabled' : ''}>
```

Same with style! (or should I say... IN STYLE🕺✨)
```pug
    Component(
        export:style
            :--some-variable="{wawiable}px"
            :margin="10px"
    )
```

### --variables
Speaking of style. You can assign CSS variables directly as attributes
```pug
    #some-div(
        --some-variable="{wariable}px"
    )
```

~~my cat is snoring loudly~~

### svelte:fragment shortcut
If you wanna skip writing the whole damn fragmentely sveltery text, you can just
```pug
    Component
        (slot="Some named slot")
            .some-div
    
    //- Becomes <svelte:fragment slot="Some named slot">...
```

### Wraps `true`, `false`, `numbers`, `arrays` into `{ }`
```pug
    div(
        some-bool=true
        another-bool=false
        some-num=123.23
        some-array=[1, 2, 3]
    )

    //- Becomes   
        <div  
            some-bool={true}
            another-bool={false}
            some-num={123.23}
            some-array={[1, 2, 3]}
        >
```

### You can comment out attributes
It removes the assigned value and indented items (relative to the commented part). 
```pug
    .some-div(
        //- some-attribute={() => {
            ...
        }}
        another-attribute={...}
        //- export:class
            :some
            :class
    )

    //- Becomes  <div class="some-div" another-attribute={...}>
```