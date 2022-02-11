<script setup lang="ts">
import { onMounted,nextTick } from 'vue'
import * as THREE from './solver/threeModule';

import { OrbitControls } from './solver/OrbitControls';
import { GLTFLoader } from './solver/GLTFLoader';

 const oninitCanvas = () => {
    nextTick(() => {
let camera, scene, renderer;

			init();
			render();

			function init() {

				const container = document.getElementById( 'app' );
				document.body.appendChild( container );

				camera = new THREE.PerspectiveCamera( 45, window.innerWidth / window.innerHeight, 0.25, 20 );
				camera.position.set( 0, 0, 3 );

				scene = new THREE.Scene();

				const envmap = new THREE.TextureLoader().load( '/src/assets/wieth.png',function(texture){
					const loader = new GLTFLoader().setPath( '/src/assets/' );
					loader.load( 'dwendwen.gltf', function ( gltf ) {
						gltf.scene.traverse( function ( child ) {
							if(child.name=="outer"||child.name=="mask"){
								child.material.envMap = texture;
								child.material.envMap.mapping = THREE.EquirectangularReflectionMapping;
								child.material.envMapIntensity=2;
							}else if(child.name=="body"){
								var map = child.material.map;
								child.material = new THREE.MeshToonMaterial({map:map});
							}
						} );

						scene.add( gltf.scene );
						render();
					} );
				});

				renderer = new THREE.WebGLRenderer( { antialias: true } );
				renderer.setPixelRatio( window.devicePixelRatio );
				renderer.setSize( window.innerWidth, window.innerHeight );
				container.appendChild( renderer.domElement );
				
				const controls = new OrbitControls( camera, renderer.domElement );
				controls.addEventListener( 'change', render ); // use if there is no animation loop
				controls.minDistance = 2;
				controls.maxDistance = 10;
				controls.target.set( 0, 0, - 0.2 );
				controls.update();

				window.addEventListener( 'resize', onWindowResize );
			}

			function onWindowResize() {
				camera.aspect = window.innerWidth / window.innerHeight;
				camera.updateProjectionMatrix();
				renderer.setSize( window.innerWidth, window.innerHeight );
				render();
			}

			function render() {
				renderer.render( scene, camera );
			}
    })
}
onMounted(() => {
           oninitCanvas()
        })
</script>

<template>
</template>

<style>
html,body{
				padding:0;
				margin:0;
			}
</style>
