# Factory Example

## Introduction
This example uses a num as the driving force behind the factory.   
The factory will not only create a material based on the type but also store it.    
This allows reuse of the same material over different objects.

## Use of the enum

1. To define parameters for getting a material.
1. Storing the different materials types separate from each other.
1. Defining functions using the enum as the convention for the function names.

The secret behind this is using the square bracket property notation.

## Example

<strong>Defining</strong>
```js
export const MaterialType = Object.freeze({
    BASIC   : "MeshBasicMaterial",
    LINE    : "MeshLineMaterial"
})

export class Materials {
    constructor() {
        this.materials = {
            [MaterialType.BASIC]: {},
            [MaterialType.LINE]: {}
        };
    }

    dispose() {
        this.materials[MaterialType.BASIC] = null;
        this.materials[MaterialType.LINE] = null;
    }

    get(type, color) {
        let material = this.materials[type][color];
        if (material == null) {
            material = this[type](color);
            this.materials[type][color] = material;
        }
        return material;
    }

    [MaterialType.BASIC](color) {
        return {type: "color"}
    }

    [MaterialType.LINE](color) {
        return {type: "line"}
    }
}
```

<strong>Using</strong>

```js
const mat = materials.get(MaterialType.BASIC, 0x000000);
```