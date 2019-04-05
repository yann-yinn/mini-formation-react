

# Formation React partie 1 - mémo

## Installation

[create-react-app](https://github.com/facebook/create-react-app) permet de créer une nouvelle applicationReact pré-configurée avec wepack, gestion des fichiers d'environnement, hot-reloading :rocket: 

`yarn install`puis `yarn start` pour commencer à travailler.

Je recommande d'installer [Prettier](https://prettier.io/) pour formater le code uniformément au sein de l'équipe. 

Le package [Storybook](https://github.com/storybooks/storybook) est également un must pour vérifier que les composants tournent bien et savoir quels composant des collègues sont *réutilisables*

Installer l'extension chrome [react-dev-tools](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi) pour inspecter le DOM virtuel et débugguer les performances.

## JSX

 `JSX` est une syntaxe raccourcie pour appeler la fonction `React.createElement()` . JSX ressemble à du html et n'est **pas** du html mais  du **Javascript**; et doit être pensé et manipulé comme tel.  

La fonction `createElement()` ne renvoie pas du html mais un **objet** qui répresente le DOM à créer plus tard, car React utilise un **DOM Virtuel** .

## Composant

Un **composant** dans sa forme le plus simple est juste une fonction :

```jsx
import React from "react"

export default function HelloWorld() {
  return (
    <div>
      Hello world !
    </div>
  )
}
```

> :warning:  Attention ! `class` devient `className` : les attributes html sont ceux utilisés en JS et peuvent différer des attributs html.
>
> :warning: Un composant est toujours nommé en **P**ascal**C**ase

`Helloworld` peut ensuite être appelé en `jsx` comme comme une

```jsx
import React, { Component } from 'react';
import HelloWorld from './components/HelloWorld'

class App extends Component {
  render() {
    return <HelloWorld />
  }
}
```

### Props

On peut passer autant de **props** que souhaité à un composant : `<HelloWorld name="Yann" />`

```jsx
export default function HelloWorld(props) {
  return (
    <div>
      Hello {props.name} !
    </div>
  )
}
```

On utilise le plus souvent l'astuce de la **destructuration** de l'objet *prop* pour expliciter les props attendues :

```jsx
export default function HelloWorld({ name }) {
  return (
    <div>
      Hello {name} !
    </div>
  )
}
```

### Prop spéciale children

**children** est une *prop* spéciale qui permet d'afficher l'enfant à l'intérieur du composant. 

```
export default function HelloWorld({ children }) {
  return (
    <div>
      Hello {children}
    </div>
  )
}
```

```jsx
class App extends Component {
  render() {
    return <HelloWorld>Yann!</HelloWorld>
  }
}
```

Affichera `Hello Yann`

`children` et n'importe quel prop peut recevoir un composant; ce qui permet de créer facilement un puissant système de Layout.

### if et conditions

Toutes les formes classiques du JS sont utilisables avec JSX

```jsx
export default function AgeControl({ age }) {
  if (age > 18) {
    return <div>You can drive a car :D </div>
  } else {
    return <div>You are too young to drive.</div>
  }
}
```

```jsx
export default function AgeControl({ age }) {
  return age > 18 ? 
    <div>You can drive a car :D </div> : 
    <div>You are too young to drive.</div>
}
```

```jsx
export default function AgeControl({ age }) {
  return (
    <div>
      <div>Can you drive ?</div>
      {age > 18 && <div>OK !</div>}
    </div>
  )
}
```

## boucles

Javascript powa' : utiliser la méthode `map`

```jsx
export default function UserList({ users }) {
  return (
    <div>
      {users.map(user => {
        return (
          <div key={user.id}>
            <h2>{user.name}</h2>
            <h3>{user.id}</h3>
          </div>
        )
      })}
    </div>
  )
}
```

## Passer des évènements au composant parent

Javascript' powa aussi : il suffit que l'enfant appel une fonction qui sera passé en props. Le composant suivant appelle la méthode `onButtonClick` du parent avec la valeur "yes" or "no".

```jsx
import React from "react"

export default function ConfirmDialog({ onButtonClick }) {
  return (
    <div class="confirm-dialog">
      <h2>Are your sure ?</h2>
      <button onClick={() => onButtonClick('Yes')}>Yes</button>
      <button onClick={() => onButtonClick('No')}>No</button>
    </div>
  )
```

De son côté, le parent doit simplement appeler la fonction de son choix dans sa prop `onButtonClick`

```jsx
class App extends Component {
  render() {
    return <ConfirmDialog onButtonClick={(anwser) => alert(anwser)} />
  }
}
```



### State

Le state permet au composant de se rafraîchir lui-même. Il est possible d'ajouter *depuis peu* le state à une fonction avec le nouveau system de hook et le hook `useState` .

Le composant ci-dessous donnera à `name` la valeur tapée dans le champ texte

```jsx
import React, { useState } from "react"

export default function HelloWorld() {
  const [name, setName] = useState('');
  return (
    <div>
      <h1>Hello {name}</h1>
      <div>
        <input type="text" onChange={(e) => setName(e.target.value)} />
      </div>
    </div>
  )
}
```

Avant les hooks, la seule manière d'ajouter un *state* à un composant était de transformer le composant en *class*

 ```jsx
import React from "react"

export default class HelloWorld extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      name: ''
    }
  }
  render() {
    return (
      <div>
        <h1>Hello {this.state.name}</h1>
        <div>
          <input type="text" onChange={(e) => this.setState({ name: e.target.value })} />
        </div>
      </div>
    )
  }
}
 ```

> :warning: Un composant devient plus difficile à gérer si il mélance mise à jour depuis les **props** et mise à jour internet à partir du **state** . Si cela arrive, vérifier si state interne ne peut pa tout simplement être passé en props depuis le parent pour avoir **une seule source de vérité** du rafraichissement du composant.



## Formulaire basique

Pour des formulaires plus avancés, je recommande [react-final-form](https://github.com/final-form/react-final-form) qui est très complète et facilement customisable.

Ci-dessous un exemple basique de formulaire en "pur" React

```jsx
import React from "react"

export default class BasicForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      name: '',
      errors: []
    }
  }
  handleSubmit = (event) => {
    event.preventDefault();
    // reset errors
    const errors = [];
    this.setState({ errors })

    // validation rules : name is required
    if (!this.state.name) {
      errors.push('Name is required')
    }

    // update state with errors to display them and abort submission
    if (errors.length > 0) {
      this.setState({ errors });
      return;
    }

    // do something with form values here if all is okay
    // this.save(this.state)

  }
  render() {
    return (
      <form onSubmit={this.handleSubmit}>

        {/* display errors, if any */}
        {this.state.errors.length > 0 &&
          <ul className="errors">
            {this.state.errors.map((error, i) => <li key={i}>{error}</li>)}
          </ul>
        }

        <input type="text" onChange={(e) => this.setState({ name: e.target.value })} />
        <input type="submit" value="save" />
      </form>
    )
  }
}

```







