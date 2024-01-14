<template>
	<div class="door-design-container">
		<div class="door-frame-container">
			<!-- Parent SVG for the frame with border -->
			<svg class="door-frame" :width="totalFrameWidth" :height="frameHeight + frameWidthTop + hingeWidth">
				<!-- Border for the frame -->
				<template v-if="showFrame && selectDoorType !== '2'">
					<rect 
						:width="frameBorder" 
						:height="frameHeight+hingeWidth" 
						:stroke="frameBorderColor"
						:x="0"
						:y="frameWidthTop"
						fill="none" 
					/>
					<rect 
						:width="frameWidth + 2 * frameBorder + 2*hingeWidth" 
						:height="frameWidthTop" 
						:stroke="frameBorderColor"
						fill="none"
					/>
					<rect 
						:width="frameBorder" 
						:height="frameHeight+hingeWidth" 
						:stroke="frameBorderColor"
						:x="frameWidth + frameBorder + hingeWidth *2"
						:y = "frameWidthTop"
						fill="none" 
					/>
				</template>
				<!--Frame for double door-->
				<template v-if="showFrame && selectDoorType == '2'">
					<rect 
						:width="frameBorder" 
						:height="frameHeight + hingeWidth" 
						:stroke="frameBorderColor"
						:x="0"
						:y="frameWidthTop"
						fill="none" 
					/>
					<rect 
						:width="frameWidth  * 2 + 2* hingeWidth + 2 * frameBorder + doorGap" 
						:height="frameWidthTop" 
						:stroke="frameBorderColor"
						fill="none" 
					/>
					<rect 
						:width="frameBorder" 
						:height="frameHeight + hingeWidth" 
						:stroke="frameBorderColor"
						:x="frameWidth * 2 + frameBorder + 2*hingeWidth + frameGap"
						:y = "frameWidthTop"
						fill="none" 
					/>			
				</template>
		
				<!-- Door panels within the frame with an equal gap on all sides -->
				<template v-if="selectDoorType">
					<rect class="door-panels"
							:width="shouldDoubleDimensions ? (frameWidth) * 2 + doorGap : frameWidth"
							:height="frameHeight"
							:fill="doorColor"
							:x="frameBorder + hingeWidth"
							:y="frameWidthTop + hingeWidth"
							:stroke="frameBorderColor" />
					<!-- Fire door sign inside a circle (conditionally added) -->
					<g v-if="fireDoorSign === '1' && !shouldDoubleDimensions">
						<!-- Circle -->
						<circle cx="50%" cy="50%" r="30" fill="#FF0000" />

						<!-- Text inside the circle -->
						<text x="50%" y="50%" text-anchor="middle" alignment-baseline="middle" font-size="10" fill="#FFFFFF">FIRE DOOR</text>
					</g>
					<!-- Fire door sign inside a circle (conditionally added) for left part of the double door -->
					<g v-if="fireDoorSign === '1' && shouldDoubleDimensions ">
						<!-- Circle -->
						<circle cx="25%" cy="50%" r="30" fill="#FF0000" />

						<!-- Text inside the circle -->
						<text x="25%" y="50%" text-anchor="middle" alignment-baseline="middle" font-size="10" fill="#FFFFFF">FIRE DOOR</text>
					</g>

					<!-- Fire door sign inside a circle (conditionally added) for right part of the double door -->
					<g v-if="fireDoorSign === '1' && shouldDoubleDimensions ">
					<!-- Circle -->
						<circle :cx=" (frameWidth - 2 * frameBorder) + ((frameWidth - 2*frameBorder)/2)" cy="50%" r="30" fill="#FF0000" />

						<!-- Text inside the circle -->
						<text :x=" (frameWidth - 2 * frameBorder) + ((frameWidth - 2*frameBorder)/2)" y="50%" text-anchor="middle" alignment-baseline="middle" font-size="10" fill="#FFFFFF">FIRE DOOR</text>
					</g>

					<template v-if="shouldDoubleDimensions">
						<rect 
							class="door-gap"
							:width="doorGap"
							:height="frameHeight"
							:x = "frameWidth + frameBorder + hingeWidth"
							:y = "frameWidthTop+hingeWidth"
							:stroke="frameBorderColor"
							fill="none"
						/>
					</template>
				</template>
		
				<g v-if="selectHandles !== '1' && !shouldDoubleDimensions ">
					<!-- Door handle -->
					<rect 
					class="door-handle" 
					width="30" 
					height="10" 
					fill="#C0C0C0" 
					:x="0.84 * frameWidth" 
					:y="frameHeight / 2" 
					:transform="handleSlantTransform" />
					<circle 
						class="handle-circle" 
						r="7" 
						fill="#C0C0C0" 
						:cx="0.94 * frameWidth"
						:cy="frameHeight / 2" 
					/>
					<rect v-if="selectHandles === '3'" 
						class="sash-lock" 
						width="15" 
						height="40" 
						fill="#C0C0C0" 
						:x="0.92 * frameWidth" 
						:y="frameHeight / 2 - 3 * hingeWidth" 
					/>
				</g>
				<g v-if="selectFireDoorCloser === '1'">
					<!-- Door closer for left -->
					<rect 
						class="door-closer" 
						:width="doorCloserWidth"
						:height="doorCloserHeight"
						fill="#C0C0C0"
						:x="frameWidth / 4" 
						:y="0"
					/>
					<!-- Door closer for Right -->
					<rect
						:v-if="shouldDoubleDimensions"
						class="door-closer" 
						:width="doorCloserWidth"
						:height="doorCloserHeight"
						fill="#C0C0C0"
						:x="((frameWidth * 2)-frameWidth * (1/4))"
						:y="0"
					/>
				</g>
				<g v-if="selectFireRatedHinges === '1'">
					<template v-for="index in totalHinges" :key="index">
						<!-- Door hinges -->
						<rect
							class="door-hinges"
							:width="hingeWidth"
							:height="hingeHeight"
							fill="#000000"
							:x="frameBorder"
							:y="frameBorder + hingeWidth + calculateHingeYPosition(index,totalHinges)"
						/>
					</template>
					<g v-if="shouldDoubleDimensions">
						<template v-for="index in totalHinges" :key="index">
							<!-- Door hinges for double door -->
							<rect
								class="door-hinges"
								:width="hingeWidth"
								:height="hingeHeight"
								fill="#000000"
								:x="2 * frameWidth + frameBorder + hingeWidth + doorGap"
								:y="frameBorder + hingeWidth + calculateHingePositionDouble(index,totalHinges)"
							/>
						</template>
					</g>
				</g>
				<g v-if="selectPanelGlazing === '1'">
						<rect 
							class="door-glazing-frame-left"
							:width="rectangularGlassFrameWidth"
							:height="rectangularGlassFrameHeight"
							fill="none"
							:stroke="frameBorderColor"
							:x="( ((frameWidth/2 - rectangularGlassFrameWidth/2) + frameBorder + hingeWidth) )"
							:y="(frameHeight/2-rectangularGlassFrameHeight/2)"
						/>
						<rect 
							v-if="shouldDoubleDimensions"
							class="door-glazing-frame-right"
							:width="rectangularGlassFrameWidth"
							:height="rectangularGlassFrameHeight"
							fill="none"
							:stroke="frameBorderColor"
							:x="( frameWidth + ((frameWidth/2 - rectangularGlassFrameWidth/2) + frameBorder + hingeWidth) + frameGap )"
							:y="(frameHeight/2-rectangularGlassFrameHeight/2)"
						/>
					<!-- Door glazer for left -->
					<rect 
						class="door-glazing-left" 
						:width="glassWidth"
						:height="glassHeight"
						fill="#d8e4e9"
						:x="((frameWidth/2 - glassWidth /2 )+frameBorder + hingeWidth)" 
						:y="(frameHeight/2-glassHeight/2)"
					/>
					<!-- Door glazer for right -->
					<rect
						:v-if="shouldDoubleDimensions"
						class="door-glazing-right" 
						:width="glassWidth"
						:height="glassHeight"
						fill="#d8e4e9"
						:x="((frameWidth + frameBorder + hingeWidth) + (frameWidth/2-glassWidth/2) + frameGap)" 
						:y="(frameHeight/2-glassHeight/2)"
					/>
				</g>


			</svg>
		</div>
		<div class="user-control">  
			<!-- User controls to toggle features -->
			<div class="user-controls">
				<label>
					<input type="checkbox" v-model="showFrame" />
						Add Frame
				</label>
				<div v-if="showFrame">
					<div class="width-input">
						<label>
							Door Width(single) in mm: <input type="number" v-model="frameWidthMM">
						</label>
						<br></br>
						<br></br>
						<label>
							Frame Width: <input type="number" v-model="frameBorderMM">
						</label>
						<br></br>
						<br></br>
						<label>
							Frame Width(Top) in mm: <input type="number" v-model="frameWidthTopMM">
						</label>
						<br></br>
						<br></br>
						<label>
							Frame Height(single) in mm: <input type="number" v-model="frameHeightMM">
						</label>
						<br></br>
						<br></br>
						<label> Select Unit:
							<input type="radio" v-model="selectUnit" value="mm">mm
						</label>
					</div>
					<div>
						<h3>Door Type</h3>
						<label>
							<input type="radio" v-model="selectDoorType" value="1">Single Door
						</label>
						<label>
							<input type="radio" v-model="selectDoorType" value="2">Double Door
						</label>
					</div>
					<br></br>
					<br></br>
					<div>
						<label>
							<input type="checkbox" v-model="enableStyles"> Do you want to enable styles?
						</label>
					</div>
					<div v-if="enableStyles">
						<h3>Door design</h3>
						<div class="color-swatch-container">
							<div class="color-swatches">
								<div
								v-for="color in colorOptions"
								:key="color"
								class="color-swatch"
								:style="{backgroundColor: color}"
								@click="selectDoorColor(color)"
								></div>
							</div>
						</div>
						<div v-if="!shouldDoubleDimensions">
							<h3>Do you like handles?</h3>
							<label>
								<input type="radio" v-model="selectHandles" value="1">No
							</label>
							<label>
								<input type="radio" v-model="selectHandles" value="2">Handles with latch
							</label>
							<label>
								<input type="radio" v-model="selectHandles" value="3">Handles with Sashlock
							</label>
						</div>
					</div>
					<div>
						<h3>Would you like fire door closer?</h3>
						<label>
							<input type="radio" v-model="selectFireDoorCloser" value="1">Yes
						</label>
						<label>
							<input type="radio" v-model="selectFireDoorCloser" value="2">No
						</label>
					</div>
					<div v-if="selectFireDoorCloser === '1'">
						<br></br>
						<label> Enter door closer width:
							<input type="number" v-model="doorCloserWidth">
						</label>
						<br></br>
						<br></br>
						<label>
							Enter door closer height:
							<input type="number" v-model="doorCloserHeight">
						</label>
					</div>
					<div>
						<h3>Would you like fire rated hinges?</h3>
						<label>
							<input type="radio" v-model="selectFireRatedHinges" value="1">Yes
						</label>
						<label>
							<input type="radio" v-model="selectFireRatedHinges" value="2">No
						</label>				
					</div>
					<div v-if="selectFireRatedHinges === '1'">
						<br></br>
						<label> Select Number of Hinges : </label>
						<select v-model="totalHinges">
							<option :value="1"> One </option>
							<option :value="2"> Two </option>
							<option :value="3"> Three </option>
							<option :value="4"> Four </option>
						</select>
					</div>
					<div>
						<h3>Would you like panel or glazings?</h3>
						<label>
							<input type="radio" v-model="selectPanelGlazing" value="1">Yes
						</label>
						<label>
							<input type="radio" v-model="selectPanelGlazing" value="2">No
						</label>				
					</div>	
					<div v-if="selectPanelGlazing === '1' ">
						<div class="glass-width-input">
						<label>
							Glass Width: <input type="number" v-model="glassWidth">
						</label>
						<br></br>
						<br></br>
						<label>
							Glass Height: <input type="number" v-model="glassHeight">
						</label>
						<br></br>
						<br></br>
						<label>
							Glass Frame Gap: <input type="number" v-model="glassFrameGap">
						</label>
						<br></br>
						<br></br>
						<label>
							Glass Frame Width: {{rectangularGlassFrameWidth}}
						</label>
						<br></br>
						<br></br>
						<label>
							Glass Frame Height: {{rectangularGlassFrameHeight}}
						</label>
					</div>
					</div>
				</div>
			</div>
		</div>
	</div>
  </template>
  
  <script>
  import { ref } from "vue";
  
  export default {
	name: "DoorDesign",
	data() {
	  return {
		frameColor: "#808080",
		panelColor: "#ffffff",
		handleColor: "#000000",
		frameBorderColor: "#000000",
		frameWidth: 100,
		frameHeight: 700,
		frameBorder: 10,
		frameGap: 5,
		showFrame: false,
		selectDoorType: "1",
		doorColor: "#ffffff",
		shouldDoubleDimensions: false,
		colorOptions: ["#ffffff","#5d2906", "#82490b", "#b76f20", "#8b6e2b", "#d4bb7e"],
		fireDoorSign: "2",
		selectHandles: "1",
		handleSlantAngle: -10,
		selectFireDoorCloser: "2",
		selectFireRatedHinges: "2",
		selectPanelGlazing: "2",
		glassWidth: 120,
		glassHeight:400,
		glassFrameGap: 20,
		rectangularGlassFrameWidth:130,
		rectangularGlassFrameHeight:410,
		hingeHeight: 30,
		totalHinges:1,
		doorCloserWidth:60,
		doorCloserHeight:40,
		doorRetainerWidth:30,
		doorRetainerHeight:50,
		enableStyles:false,
		selectUnit:"mm",
		hingeWidth: 5,
		doorGap:5,
		frameWidthMM:900,
		frameHeightMM:1800,
		frameBorderMM:32,
		frameWidthTop:10,
		frameWidthTopMM: 32,
	  };
	},
	computed: {
	  totalFrameWidth() {
		return this.shouldDoubleDimensions ? this.frameWidth * 2 + this.frameBorder*2 + this.hingeWidth*2 + this.doorGap : this.frameBorder*2 + this.frameWidth + this.hingeWidth * 2;
	  },
	  handleSlantTransform(){
		const radians = (this.handleSlantAngle * Math.PI) / 180;
		const translateY = this.frameHeight / 2 + this.frameBorder - 10;
		return `rotate(${this.handleSlantAngle} ${this.frameWidth + this.frameBorder * 2 - this.frameWidth / 4} ${translateY})`;
	  },
	},
	watch: {
		selectDoorType(newType) {
			this.shouldDoubleDimensions = newType === '2';
		},
		glassWidth() {
      		this.updateRectangularGlassFrameWidth();
    	},
		glassHeight() {
			this.updateRectangularGlassFrameHeight();
		},
		glassFrameGap(newGap) {
			this.updateRelatedValues();
		},
		frameWidthMM() {
			this.updatedFrameWidthMM();
		},
		frameHeightMM() {
			this.updatedFrameHeightMM();
		},
		frameBorderMM() {
			this.updatedFrameBorderMM();
		},
		frameWidthTopMM() {
			this.updatedFrameWidthTopMM();
		}
	},
	methods: {
		selectDoorColor(color){
			this.doorColor = color;
		},
		calculateHingeYPosition(index, totalHinges) {

			if (totalHinges === 3) {
				if (index <= 2) {
					return ((this.frameHeight / 2) / 3) * index;
				} else {
				// Place the third hinge on the bottom third of the door
					return this.frameHeight - (1 / 3 * (this.frameHeight));
				}
			} else if (totalHinges === 2) {
				if (index === 1) {
					return (((this.frameHeight / 2) * 2) / 3);
				} else {
					return ((this.frameHeight * 2) / 3);
				}
			} else if ( totalHinges === 1){
				return (this.frameHeight / 2);
			} else if( totalHinges === 4 ){
				if (index <= 2) {
					if(index === 1){
						return ((this.frameHeight / 2) /3) - this.frameBorder;
					} else {
						return ((this.frameHeight / 2) / 3) * index - this.frameBorder;
					}
				} else {
					if(index === 3){
						return ((((this.frameHeight / 2) * 2) + this.frameHeight) / 3) - this.frameBorder;						
					} else {
						return (((this.frameHeight/2) + 2 * this.frameHeight ) / 3) - this.frameBorder;
					}
				}
			}
		},
		calculateHingePositionDouble(index, totalHinges){
			if (totalHinges === 3) {
				if (index <= 2) {
					return ((this.frameHeight / 2) / 3) * index;
				} else {
					return this.frameHeight - (1 / 3 * (this.frameHeight));
				}
			} else if (totalHinges === 2) {
				if (index === 1) {
					return (((this.frameHeight / 2) * 2) / 3);
				} else {
					return ((this.frameHeight * 2) / 3);
				}
			} else if ( totalHinges === 1){
				return (this.frameHeight / 2);
			} else if( totalHinges === 4 ){
				if (index <= 2) {
					if(index === 1){
						return ((this.frameHeight / 2) /3) - this.frameBorder;
					} else {
						return ((this.frameHeight / 2) / 3) * index - this.frameBorder;
					}
				} else {
					if(index === 3){
						return ((((this.frameHeight / 2) * 2) + this.frameHeight) / 3) - this.frameBorder;						
					} else {
						return (((this.frameHeight/2) + 2 * this.frameHeight ) / 3) - this.frameBorder;
					}
				}
			}
		},
		updateRelatedValues() {
			this.rectangularGlassFrameWidth = this.glassWidth + this.glassFrameGap;
			this.rectangularGlassFrameHeight = this.glassHeight + this.glassFrameGap;
    	},
		updateRectangularGlassFrameWidth() {
			this.rectangularGlassFrameWidth = this.glassWidth + this.glassFrameGap;
    	},
		updateRectangularGlassFrameHeight() {
			this.rectangularGlassFrameHeight = this.glassHeight + this.glassFrameGap;
    	},
		updatedFrameWidthMM() {
      		this.frameWidth = this.frameWidthMM / 3;
    	},
		updatedFrameHeightMM() {
			this.frameHeight = this.frameHeightMM / 3;
		},
		updatedFrameBorderMM() {
			this.frameBorder = this.frameBorderMM / 3;
		},
		updatedFrameWidthTopMM(){
			this.frameWidthTop = this.frameWidthTopMM / 3;
		}
	},
  };
  </script>
  
  <style scoped>
	.color-inputs,
	.size-inputs {
	margin-top: 20px;
	}

	.door-frame {
		position: relative;
	}

	.color-swatch-container {
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.color-swatches {
		display: flex;
		gap: 5px;
	}

	.color-swatch {
		width: 20px;
		height: 20px;
		cursor: pointer;
	}
	.door-frame-container {
		position: fixed;
		top: 0;
		order:1;
		left: 50%;
		transform: translateX(-50%);
		z-index: 1000;
	}
	.user-controls{
		padding-top:20px;
		order:2;
		padding-left: 20px;
	}
	.door-design-container {
  		display: flex;
	}
  </style>
  