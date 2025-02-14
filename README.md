[![Join the chat at https://gitter.im/JordanDelcros/OBJImg](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/JordanDelcros/OBJImg?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
[[![npm version](https://badge.fury.io/js/objimg.svg)](https://badge.fury.io/js/objimg)](https://www.npmjs.com/package/objimg)

# OBJIMG

Convert OBJ/MTL files (exported from a 3D soft) into a lightweight image ready for THREE JS (or native WebGL).

## Wait! what?
Ok, an OBJ file contains all informations about the 3D model: vertices, faces, normals, UVs, groups and materials...
All these informations are translated into colours and stored into one single image.

## Why?
First of all, for the fun!

Then cause it save disk space (the compression method can save up to 80% on the file size, or maybe more) and it reduce the files to load from 2 (OBJ and MTL) to only 1 (except textures).

## Example
![sample schema](examples/resources/schema.jpg)

This Lara Croft 3D model contains 74764 vertices, 48549 uvs, 74690 normals and 143290 faces dispatched in 12 differents groups.
On the left, you can see the OBJ/MTL into Blender, at center, the compressed image containing all datas, and on the right, the THREE object builded from the image.

As you can see, the two rendered models looks similar but there is a huge difference, their sizes.
The OBJ/MTL files weigh 13.4Mo and the compressed image only weigh 3.2Mo!

## How to?
The `OBJImg` Class contains both methods to parse and generate the images.

#### Import 3D model from image
To import the model from an image, link the `objimg.js` script to your html then do this:
```javascript
new OBJImg({
	image: "path/to/model.png",
	onComplete: function( datas ){

		console.log("If requested, THREE object has been created", datas);
	
	}
});
```

#### Options & methods
##### Options
 - **image:** path to the image
 - **useWorker:** [boolean] define if the script will use worker to avoid main-thread freezing (default false)
 - **reveiveShadow:** [boolean] define if the THREE-JS object will receive shadows (default false)
 - **castShadow:** [boolean] define if the THREE-JS object will cast shadows (default false)
 - **onComplete:** [function(datas)] called when the datas are successfuly parsed from the image
 - **onError:** [function(error)] called when an error occurs

##### Methods
 - **getObject3D:** [callback] return a complete THREE-JS `Object3D` (require THREE-JS)
 - **getSimpleObject3D:** [callback] return a THREE-JS `Object3D` ignoring groups (require THREE-JS)

#### Use with THREE-JS (>= r73)
```javascript
var model = new OBJImg({
	image: "path/to/model.png"
}).getObject3D();

scene.add(model);
```

### Generate image from 3D model
To generate an image model, you can use the `OBJImg` Class script or the Command Line Interface.

#### Using the Command Line Interface

All informations are on [the npm page](https://www.npmjs.com/package/objimg).

#### Using the Class script
It is very easy to implement, just link the `objimg.js` script to your html then do this:
```javascript
OBJImg.generateIMG({
	obj: "path/to/file.obj",
	useWorker: true,
	onComplete: function( datas ){
	
		var image = new Image();
		image.src = datas;
	
	},
	onError: function( error ){
	
		console.error(error);
	
	}
});
```
When an image is created, you can access it in the developer tools over resources tab, or you can append it to the DOM to save it or drag it.

##### Options
 - **obj:** the path to the OBJ file or the OBJ content itself
 - **mtl:** the MTL file content (optional, only if the obj is content and not a path)
 - **useWorker:** [boolean] definning if the script is executed into a webworker to avoid main-thread freezing
 - **onComplete:** [function(datas)] callback function with datas parameters (datas is is base64 encoded image)
 - **onError:** [function(error)] callback function when the script fail to generate image

 If the `obj` parameter is a path, the script will parse the content for a MTL lib (path to the MTL).

##### OBJ support
 - **v**: [x y z] vertex coordinates
 - **vt**: [x y z] uv coordinates
 - **vn**: [x y z] normal direction
 - **f**: [v/vt/vn v/vt/vn v/vt/vn] face infos (only support triangles yet)
 - **o**: [name] of new object
 - **g**: [name] of new group
 - **mtllib**: [path] to material library to use
 - **usemtl**: [name] of material to use

##### MTL support
 - **newmtl**: [name] of new material
 - **ka**: [r g b] ambient color
 - **kd**: [r g b] diffuse color
 - **ks**: [r g b] specular color
 - **ns**: [float] specular force
 - **ne**: [float] reflectivity force
 - **d**: [float] opacity
 - **illum**: [1|2|3|4|5|6|7|8|9|10] illumination mode
 - **s**: [on|off] smooth or flat rendering
 - **map_ka**: [path] to ambient occlusion texture
 - **map_kd**: [path] to diffuse texture (albedo)
 - **map_ks**: [path] to specular texture
 - **map_ns**: [path] to specular force texture
 - **map_kn**: [path] to normal texture (none standard)
 - **map_ke**: [path] to environement texture (none standard)
 - **map_bump**: [path] to bump texture
 - **map_d**: [path] to opacity texture
 - **shader_s**: [front|back|double] side to render (none standard)
 - **shader_v**: [path] to vertex shader (none standard, support all phong uniforms)
 - **shader_f**: [path] to fragment shader (none standard, support all phong uniforms)

## Do not compress images

Each pixel represent a precise number between 0 and 65280 (255 * 255 + 255 | red * green + blue) so if you compress the image with less, pixels color will be alterate and datas too...
You cant use image compression except lossless compression like OptiPNG. (The objimg CLI already compress the image with the strongest method of OptiPNG).

## Todo
 - support quadratic face parsing
 - support custom shaders
 - use `src` for image and obj parameters, make it more common