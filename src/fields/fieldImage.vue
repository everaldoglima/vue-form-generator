<template lang="jade">
	div.wrapper
		input.form-control.link(type="text", v-model="wrappedValue", :readonly="schema.readonly", :disabled="disabled", :placeholder="schema.placeholder")
		input.form-control.file(type="file", :readonly="schema.readonly", :disabled="disabled", v-if="schema.browse !== false", @change="fileChanged")
		.preview(:style="previewStyle")
			.remove.fa.fa-trash-o(title="Remove image", @click="remove")
</template>

<script>
	import abstractField from "./abstractField";

	export default {
		mixins: [ abstractField ],

		computed: {
			previewStyle() {
				if (this.schema.preview !== false) {
					return {
						display: "block",
						"background-image": "url(" + this.value + ")"
					};
				} else {
					return {
						display: "none"
					};
				}
			},

			wrappedValue: {
				get() {
					if (this.value && this.value.indexOf("data") == 0)
						return "<inline base64 image>";
					else
						return this.value;
				},
				set(newValue) {
					if (newValue && newValue.indexOf("http") == 0) {
						this.value = newValue;
					}
				}
			}
		},

		watch: {
			model() {
				$(this.$el).find("input.file").val("");
			}
		},

		methods: {
			remove() {
				this.value = "";
			},

			fileChanged(event) {
				let reader = new FileReader();
				reader.onload = (e) => {
					this.value = e.target.result;				
				};

				if (event.target.files && event.target.files.length > 0) {
					reader.readAsDataURL(event.target.files[0]);
				}
			}
		}
	};
</script>

<style lang="sass" scoped>
	.wrapper, input {
		width: 100%;
	}

	.preview {
		position: relative;
		margin-top: 5px;
		height: 100px;
		background-repeat: no-repeat;
		background-size: contain;
		background-position: center center;						
		border: 1px solid #ccc;
		border-radius: 3px;
		box-shadow: inset 0 1px 1px rgba(0, 0, 0, .075);

		.remove {
			/*background-image: url('data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABAAAAAQCAYAAAAf8/9hAAAAXUlEQVR42u2SwQoAIAhD88vVLy8KBlaS0i1oJwP3piGVg0Skmpq8HjqZrWl9uwCbGAmwKYGZs/6iqgMyAdJuM8W2QmYKpLt/0AG9ASCv/oAnANd3AEjmAlFT1BypAV+PnRH5YehvAAAAAElFTkSuQmCC');
			width: 16px;
			height: 16px;
			*/
			font-size: 1.2em;

			position: absolute;
			right: 0.2em;
			bottom: 0.2em;
			opacity: 0.7;

			&:hover {
				opacity: 1.0;
				cursor: pointer;
			}
		}
	}
</style>
