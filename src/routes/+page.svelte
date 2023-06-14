<script>
	import { onMount, afterUpdate } from 'svelte';
	import interact from 'interactjs';
	import { writable } from 'svelte/store';

	const position = writable({ x: 0, y: 0, scaleX: 136, scaleY: 136 });
	$: console.log($position);

	onMount(async () => {
		// target elements with the "draggable" class
		interact('.draggable')
			.resizable({
				// resize from all edges and corners
				edges: { left: true, right: true, bottom: true, top: true },

				listeners: {
					move(event) {
						var target = event.target;
						var x = parseFloat(target.getAttribute('data-x')) || 0;
						var y = parseFloat(target.getAttribute('data-y')) || 0;

						// update the element's style
						target.style.width = event.rect.width + 'px';
						target.style.height = event.rect.height + 'px';

						// Custom Code
						$position.scaleX = event.rect.width;
						$position.scaleY = event.rect.height;

						// translate when resizing from top or left edges
						x += event.deltaRect.left;
						y += event.deltaRect.top;

						target.style.transform = 'translate(' + x + 'px,' + y + 'px)';

						target.setAttribute('data-x', x);
						target.setAttribute('data-y', y);
						// target.textContent =
						// 	Math.round(event.rect.width) + '\u00D7' + Math.round(event.rect.height);
					}
				},
				modifiers: [
					// keep the edges inside the parent
					interact.modifiers.restrictEdges({
						outer: 'parent'
					}),

					// minimum size
					interact.modifiers.restrictSize({
						min: { width: 100, height: 50 }
					})
				],

				inertia: true
			})
			.draggable({
				// enable inertial throwing
				inertia: true,
				// keep the element within the area of it's parent
				modifiers: [
					interact.modifiers.restrictRect({
						restriction: 'parent',
						endOnly: true
					})
				],
				// enable autoScroll
				autoScroll: true,

				listeners: {
					// call this function on every dragmove event
					move: dragMoveListener,

					// call this function on every dragend event
					end(event) {}
				}
			});

		function dragMoveListener(event) {
			var target = event.target;
			// keep the dragged position in the data-x/data-y attributes
			var x = (parseFloat(target.getAttribute('data-x')) || 0) + event.dx;
			var y = (parseFloat(target.getAttribute('data-y')) || 0) + event.dy;

			// Custom Code
			$position.x = x;
			$position.y = y;

			// translate the element
			target.style.transform = 'translate(' + x + 'px, ' + y + 'px)';

			// update the posiion attributes
			target.setAttribute('data-x', x);
			target.setAttribute('data-y', y);
		}

		// this function is used later in the resizing and gesture demos
		window.dragMoveListener = dragMoveListener;
	});
</script>

<main>
	<div class="border w-[1280px] h-[720px] bg-gray-400 relative">
		<div id="drag-1" class="draggable w-36 h-36 border-4 border-red-600 absolute text-red-800">
			Draggable Element
		</div>
		<!-- <div id="drag-2" class="draggable w-36 h-36 border-4 border-red-600 absolute text-red-800">
			Draggable Element
		</div>
		<div id="drag-3" class="draggable w-36 h-36 border-4 border-red-600 absolute text-red-800">
			Draggable Element
		</div>
		<div id="drag-4" class="draggable w-36 h-36 border-4 border-red-600 absolute text-red-800">
			Draggable Element
		</div> -->
	</div>
</main>
