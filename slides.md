---
theme: apple-basic
highlighter: shiki
lineNumbers: false
drawings:
  persist: false
title: Panda CSS
mdc: true
monaco: true
monacoTypesSource: local # or cdn or none
layout: intro
---

# Panda CSS is better thank you think

---

# Panda is too verbose

---

# Style properties and breakpoints are at the same depth

---

# Group by style property

<div class="mt-10" />

```tsx
import { css } from "styled-system/css";

css({
  bg: {
    base: "red.100",
    _hover: "red.200",
    _focus: "red.300",
  },
});
```

---

# Group by conditional styles

<div class="mt-10" />

```tsx
import { css } from "styled-system/css";

css({
  fontSize: "2xl",
  bg: "red.100",
  _focus: {
    bg: "red.300",
    fontSize: "3xl",
  },
  _hover: {
    bg: "red.200",
  },
});
```

---

# Even more mixing...


```tsx
import { css } from "styled-system/css";

css({
  bg: "red.100",
  _focus: {
    bg: "red.200",
  },
  _hover: {
    bg: "red.300",
  },
  sm: {
    bg: "red.200",
    _hover: {
      bg: "red.400",
    },
  },
  md: {
    bg: "red.300",
    _hover: {
      bg: "red.500",
    },
  },
});
```

---

# Wrap base styles under the `base` property

```tsx
import { css } from "styled-system/css";

css({
  sm: {
    bg: "red.100",
  },
  md: {
    fontSize: "2xl",
    bg: "red.200",
  },
  base: {
    fontSize: "xl",
  },
});
```

---

# How Panda detects the styles you use

---

# Tailwind searches for exact class name strings

```tsx
// ❌ Won't work: Tailwind won't be able to detect which classes are used at runtime.
function AppBadge({ color, children }) {
  return (
    <span
      className={`inline-flex items-center font-medium bg-${color}-100 text-${color}-800`}
    >
      {children}
    </span>
  );
} 
```

---

# Panda supports this...

```tsx
import { css } from '../styled-system/css'

const Button = (props) => {
  const [isSelected, setIsSelected] = useState(false)

  return (
    <button
      // ✅ the static extractor will detect both `blue.500` and `yellow.500`
      // and will generate the CSS for both
      // ℹ️ you can nest conditions indefinitely and it would still be fine
      className={css({ color: isSelected ? 'blue.500' : 'yellow.500' })}
    >
      {props.children}
    </button>
  )
}
```

---

# And this...

```tsx
import { css } from '../styled-system/css'

const colors = {
  blue: 'blue.500',
  yellow: 'yellow.500',
}
const base = { color: colors.blue } // ✅ this will be resolved inside of the `css`
const selected = { color: colors['yellow'] } // ✅ this will also be resolved

const Button = (props) => {
  const isSelected = props.isSelected

  return (
    <button
      className={css({
        ...base,
        // ✅ you can mix spread and conditions
        ...(isSelected ? selected : { fontSize: '12px' }),
      })}
    >
      {props.children}
    </button>
  )
}
```

---

# Tokens and semantic tokens

---

<div class="-mt-9">

```tsx
// panda.config.ts
import { defineConfig } from '@pandacss/dev'

export default defineConfig({
  theme: {
    semanticTokens: {
      colors: {
				ui: {
	        background: {
	          DEFAULT: {
							value: { base: '{colors.white}', _dark: '{colors.gray.950}' },
						},
						secondary: {
							value: { base: '{colors.gray.100}', _dark: '{colors.gray.900}' },
						}
	        },
	        text: {
	          DEFAULT: {
							value: { base: '{colors.slate.900}', _dark: '{colors.slate.200}' },
						},
						secondary: {
							value: { base: '{colors.slate.800}', _dark: '{colors.slate.400}' },
						}
	        }
				}
      }
    }
  }
})
```

</div>

---

# Use semantic tokens with the `css` function

```tsx
import { css } from '../styled-system/css'
 
function App() {
	const theme = useTheme()

  return (
		// Will change the resolved value of the semantic tokens 👇
		<html className={theme === 'dark' ? 'dark' : 'light'}>
	    <div
	      className={css({
	        bg: 'ui.background',
					color: 'ui.text.secondary',
	      })}
	    >
	      Hello World
	    </div>
		</html>
  )
}
```

---

# Optional JSX components

---

# JSX support

```tsx
import { styled } from '../styled-system/jsx'

const Button = ({ children }) => (
  <styled.button bg="blue.500" color="white" py="2" px="4" rounded="md">
    {children}
  </styled.button>
)
```

---

<div class="-mt-5">

```tsx
import { styled } from '../styled-system/jsx'
 
const Button = styled('button', {
  base: {
    py: '2',
    px: '4',
    rounded: 'md'
  },
  variants: {
    variant: {
      primary: {
        bg: 'blue.500',
        color: 'white'
      },
      secondary: {
        bg: 'gray.500',
        color: 'white'
      }
    }
  }
})
 
const App = () => (
  <Button variant="secondary" mt="10px">
    Button
  </Button>
)
```

</div>

---

<div class="-mt-8">

```tsx
import { cva, type RecipeVariantProps } from "./styled-system/css";

const appBadge = cva({
  base: {
    display: "inline-flex",
    alignItems: "center",
    fontWeight: "medium",
  },
  variants: {
    status: {
      success: {
        color: "green.800",
        bg: "green.100",
      },
      pending: {
        color: "yellow.800",
        bg: "yellow.100",
      },
    },
  },
});

const App = () => {
  return (
    <span className={appBadge({ status: "pending" })}>
      Waiting for Theo's response to my response to Theo
    </span>
  );
}
```

</div>

---

# Patterns

```tsx
import { vstack } from '../styled-system/patterns'
 
function App() {
  return (
    <div className={vstack({ gap: '6' })}>
      <div>First</div>
      <div>Second</div>
      <div>Third</div>
    </div>
  )
}
```

```tsx
import { grid } from '../styled-system/patterns'
 
function App() {
  return (
    <div className={grid({ columns: 3, gap: '6' })}>
      <div>First</div>
      <div>Second</div>
      <div>Third</div>
    </div>
  )
}
```

---

# JSX components for patterns

```tsx
import { Stack, Circle } from '../styled-system/jsx'

const App = () => (
  <Stack gap="4" align="flex-start">
    <button>Button</button>
    <Circle size="4" bg="red.300">4</Circle>
  </Stack>
)
```

---

<div class="-mt-6">

```tsx
import { css, sva } from '../styled-system/css'
 
const checkbox = sva({
  slots: ['root', 'control', 'label'],
  base: {
    root: { display: 'flex', alignItems: 'center', gap: '2' },
    control: { borderWidth: '1px', borderRadius: 'sm' },
    label: { marginStart: '2' }
  },
  variants: {
    size: {
      sm: {/** */},
      md: {/** */}
    }
  },
})

const Checkbox = () => {
  const classes = checkbox({ size: 'sm' })
  return (
    <label className={classes.root}>
      <input type="checkbox" className={css({ srOnly: true })} /> {/* ← */}
      <div className={classes.control} />
      <span className={classes.label}>Checkbox Label</span>
    </label>
  )
}
```

</div>
