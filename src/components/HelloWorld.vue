<template>
    <div class="hello">
        <h1>{{ msg }}</h1>
    </div>
</template>

<script>
import * as PIXI from "pixi.js";
export default {
    name: "HelloWorld",
    props: {
        msg: String,
    },
    mounted() {
        var size = [1920, 1080];
        var ratio = size[0] / size[1];
        var app = new PIXI.Application();
        var renderer = PIXI.autoDetectRenderer(size[0], size[1], null);
        document.body.appendChild(renderer.view);

        resize();
        function resize() {
            var w;
            var h;
            if (window.innerWidth / window.innerHeight >= ratio) {
                w = window.innerHeight * ratio;
                h = window.innerHeight;
            } else {
                w = window.innerWidth;
                h = window.innerWidth / ratio;
            }
            renderer.view.style.width = w + "px";
            renderer.view.style.height = h + "px";
        }
        window.onresize = function(event) {
            console.log(event);
            resize();
        };

        document.body.appendChild(renderer.view);

        // holder to store the aliens
        const aliens = [];

        const totalDudes = 20;

        for (let i = 0; i < totalDudes; i++) {
            // create a new Sprite that uses the image name that we just generated as its source
            const dude = PIXI.Sprite.from(require("../assets/eggHead.png"));

            // set the anchor point so the texture is centerd on the sprite
            dude.anchor.set(0.5);

            // set a random scale for the dude - no point them all being the same size!
            dude.scale.set(0.8 + Math.random() * 0.3);

            // finally lets set the dude to be at a random position..
            dude.x = Math.random() * app.screen.width;
            dude.y = Math.random() * app.screen.height;

            dude.tint = Math.random() * 0xffffff;

            // create some extra properties that will control movement :
            // create a random direction in radians. This is a number between 0 and PI*2 which is the equivalent of 0 - 360 degrees
            dude.direction = Math.random() * Math.PI * 2;

            // this number will be used to modify the direction of the dude over time
            dude.turningSpeed = Math.random() - 0.8;

            // create a random speed for the dude between 2 - 4
            dude.speed = 2 + Math.random() * 2;

            // finally we push the dude into the aliens array so it it can be easily accessed later
            aliens.push(dude);

            app.stage.addChild(dude);
        }

        // create a bounding box for the little dudes
        const dudeBoundsPadding = 100;
        const dudeBounds = new PIXI.Rectangle(
            -dudeBoundsPadding,
            -dudeBoundsPadding,
            app.screen.width + dudeBoundsPadding * 2,
            app.screen.height + dudeBoundsPadding * 2
        );

        app.ticker.add(() => {
            // iterate through the dudes and update their position
            for (let i = 0; i < aliens.length; i++) {
                const dude = aliens[i];
                dude.direction += dude.turningSpeed * 0.01;
                dude.x += Math.sin(dude.direction) * dude.speed;
                dude.y += Math.cos(dude.direction) * dude.speed;
                dude.rotation = -dude.direction - Math.PI / 2;

                // wrap the dudes by testing their bounds...
                if (dude.x < dudeBounds.x) {
                    dude.x += dudeBounds.width;
                } else if (dude.x > dudeBounds.x + dudeBounds.width) {
                    dude.x -= dudeBounds.width;
                }

                if (dude.y < dudeBounds.y) {
                    dude.y += dudeBounds.height;
                } else if (dude.y > dudeBounds.y + dudeBounds.height) {
                    dude.y -= dudeBounds.height;
                }
            }
        });
    },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
    margin: 40px 0 0;
}
ul {
    list-style-type: none;
    padding: 0;
}
li {
    display: inline-block;
    margin: 0 10px;
}
a {
    color: #42b983;
}
</style>
