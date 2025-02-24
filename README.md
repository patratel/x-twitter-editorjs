# X and Twitter Embed Plugin for Editor.js

Fork from https://github.com/Aror/twitter-embed-editorjs-plugin#readme that covers also the new x url

## Note

This package requires that Twitter for Websites JavaScript library is **loaded before Editor.js is initialized**

## Instructions

**Get the package**

```shell
npm i x-twitter-embed-editorjs-plugin
```

**Create Twitter library handler**

```JavaScript
const TwitterScriptHandler = () => {
  const script = document.createElement('script')

  return {
    initialize() {
      return new Promise<void>(resolve => {
        script.src = 'https://platform.twitter.com/widgets.js'
        document.body.appendChild(script)
        script.setAttribute('data-testid', 'twitter-script')
        script.onload = () => resolve()
      })
    },
    cleanup() {
      document.body.removeChild(script)
    }
  }
}
```
This is used to load and destroy Twitter script

**Load Twitter before initializing Editor.js**
```JavaScript
const App = () => {
  let editor = useRef<EditorJS>()

  useEffect(() => {
    const twitter = TwitterScriptHandler()

    twitter.initialize().then(() => {
      editor.current = new EditorJS({...editorConfiguration})
    })

    return function cleanup() {
      editor.current?.destroy?.()
      twitter.cleanup()
    }
  }, [])

  return (
    <>
      <div id='editor'/>
    </>
  )
}
```
When the component is rendered, it will first check to see if Twitter library is initialized. Only after it is initialized, Editor.js will be rendered.

## Complete Example

```javascript
import { useRef, useEffect } from 'react'
import EditorJS, { EditorConfig } from '@editorjs/editorjs'
import Twitter from 'x-twitter-embed-editorjs-plugin'

const editorConfiguration: EditorConfig = {
  holder: 'editor',
  placeholder: 'Start writing here...',
  tools: {
    twitter: {
      class: Twitter
    },
  }
}

const TwitterScriptHandler = () => {
  const script = document.createElement('script')

  return {
    initialize() {
      return new Promise<void>(resolve => {
        script.src = 'https://platform.twitter.com/widgets.js'
        document.body.appendChild(script)
        script.setAttribute('data-testid', 'twitter-script')
        script.onload = () => resolve()
      })
    },
    cleanup() {
      document.body.removeChild(script)
    }
  }
}

const App = () => {
  let editor = useRef<EditorJS>()

  useEffect(() => {
    const twitter = TwitterScriptHandler()

    twitter.initialize().then(() => {
      editor.current = new EditorJS({...editorConfiguration})
    })

    return function cleanup() {
      editor.current?.destroy?.()
      twitter.cleanup()
    }
  }, [])

  return (
    <>
      <div id='editor'/>
    </>
  )
}

export default App
```
