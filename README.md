# virtualsciencelab

var chemistry = function(lab) {};

  

/* Instructions screen */
var instructionCard, content, heading, nextButton = null;

/* lab wear */
var isCoat = false, isGloves = false,  isGoggles = false;
var labCoat, goggles, gloves;

/* apparatus */
var apparatus;
var beaker, burette, burette_tap, conical_flask, HCl2, hcl_cork,beakertagged,conical_flasktagged,
    graduated_cylinder, NaOH_cork, NaOh2, phenolphthalein_bottle, phenolphthalein_dipper,
    pipette, retot_stand_and_cork;
var isBeaker = false, isBurette = false, isBurette_tap = false, isConical_flask = false, isHCl2 = false, isHcl_cork = false,
    isGraduated_cylinder = false, isNaOH_cork = false, isNaOh2 = false, isPhenolphthalein_bottle = false, isPhenolphthalein_dipper = false,
    isPipette = false, isRetot_stand_and_cork,isbeakertagged = false, isconical_flasktagged = false; 
var container_group;    
beaker_tween = null;  
conical_flask_tween = null;
chemistry.prototype = {
  init: function() {},
  preload: function() {},
  create: function() {
    //chemistry practicals from json index 0 (first expirement)
    var practicals = jsonData.chemistry[0];

    //background inage
    addImage(0, 0, "background", false, "", false, 1);
    
    //background sound
    backgroundSound(true);

    //back button
    var chemistryBackButton = addImage(108.5,40,"chemistryBackButton",true,"clickable", true, 1);
    chemistryBackButton.events.onInputDown.add(function() {
      speakSound("click");
      lab.state.start("selection");
    }, this);

    /* TITLES */

    //reading titles from json

    //title
    var title = lab.add.text(lab.world.centerX, 50, practicals.title, 
    { font: '40px Arial', fill: '#FFFFFF',wordWrap: true, wordWrapWidth: 500,align: 'center'});
    title.anchor.setTo(.5, 1);
    //expirement
    var experiment = lab.add.text(lab.world.centerX, 100, practicals.experiment, 
      { font: '25px Arial', fill: '#FFFFFF',wordWrap: true, wordWrapWidth: 600,align: 'center'});
      experiment.anchor.setTo(.5, 1);

      //Chemistry ambience
  var soundOn = addImage(1170.5, 36.5, "soundOn", true, "clickable", true, 1);
  soundOn.events.onInputDown.add( function () {
      speakSound('click');
      soundOff.visible = true;
      soundOn.visible = false;
      ambiance = false;
  }, this);
  var soundOff = addImage(1170.5, 36.5, "soundOff", true, "clickable", true, 1);
  soundOff.visible = false;
  soundOff.events.onInputDown.add( function () {
      speakSound('click');
      soundOff.visible = false;
      soundOn.visible = true;
      ambiance = true;
  }, this);

    /* INSTRUCTIONS */  

    //instructions card (background)
    var Graphics = new Phaser.Graphics();
        Graphics.beginFill(0xffffff, .7);
        Graphics.drawRoundedRect(0, 0, 330, 266, 5);
        Graphics.endFill();
    instructionCard = addImage(190.50, 210, Graphics.generateTexture(), false, "", true, 1);

    //instructions heading
    heading = lab.add.text(-150, -120, '', 
      { font: '22px Arial', fill: '#000000',wordWrap: true, wordWrapWidth: instructionCard.width - 50});
    
    //instruction content
    content = lab.add.text(-150, -80, '', 
    { font: '18px Arial', fill: '#000000',wordWrap: true, wordWrapWidth: instructionCard.width - 50});

    //adding heading and content as children of the instruction card
    instructionCard.addChild(heading);
    instructionCard.addChild(content);


    //read instructions from json
    for(var i = 0; i < practicals.instructions.length; i++){
       var instructions = practicals.instructions[i];
       if(instructions.title === 'preparation') {
        
        //appending name to the heading text
        heading.text = instructions.name;

        //next button
        nextButton = addImage(40, 70,"nextButton",true,"clickable", false, .6);
        nextButton.case = 'steps'
        nextButton.instructions = instructions;
        nextButton.num = 0;
        nextButton.events.onInputDown.add( executeStep, this);
        nextButton.visible = false;
        instructionCard.addChild(nextButton);

        //reading content in the form of typing
        nextLine(instructions.steps, 0, instructions.title);
       }
    }

    /* Add character */
    addImage(102, 533, "normal_character", false, "", true, 1);

    //lab coat
    labCoat = addImage(1170, 525, "lab_coat", true, "draggable", true, 1);
    var labCoat2 = addImage(102.5, 506, "lab_coat2", false, "", true, 1);
    labCoat2.visible = false;
    labCoat.events.onDragStop.add(function() {
      if (labCoat2.overlap(labCoat)) {
        lab.add
          .tween(labCoat)
          .to({ x: 102.5, y: 506 }, 1000, "Quart.easeOut", true);
        setTimeout(function() {
          labCoat2.visible = true;
          labCoat.visible = false;
          isCoat = true;
        }, 1000);
      } else {
        labCoat.x = 1170;
        labCoat.y = 525;
      }
    });


    //goggles
   goggles = addImage(1170, 384, "goggles", true, "draggable", true, 1);
    var goggles_area = addImage(100, 376.5, "goggles_area", false, "", true, 1);
    goggles.events.onDragStop.add(function() {
      if (goggles.overlap(goggles_area)) {
        lab.add
          .tween(goggles)
          .to({ x: 99, y: 378 }, 1000, "Quart.easeOut", true);
        goggles.inputEnabled = false;
        isGoggles = true;
      } else {
        goggles.x = 1170;
        goggles.y = 384;
      }
    });

    //gloves
    gloves = addImage(1170, 683, "gloves", true, "draggable", true, 1);
    var gloves_area = addImage(102, 541.5, "gloves_area", false, "", true, 1);
    gloves.events.onDragStop.add(function() {
      if (gloves.overlap(gloves_area)) {
        lab.add.tween(gloves).to({ x: 102, y: 538 }, 1000, "Quart.easeOut", true);
        isGloves = true;
        gloves.inputEnabled = false;
      } else {
        gloves.x = 1170;
        gloves.y = 683;
      }
    });


    //hidding all lab wears
    labCoat.visible = false;
    goggles.visible = false;
    gloves.visible = false;

    /* APPARATUS */
    apparatus = [];
    container_group = lab.add.group();
    var beaker_c = addImage(1235, 131, "aparatus_container", false, "", true, 1);
    beaker = addImage(0, 0, "beaker", true, "draggable", true, .25);
    beaker_c.addChild(beaker);
    container_group.add(beaker_c);
    apparatus.push(beaker);
    beaker.name = 'beaker';

    beaker.events.onDragStop.add(function() {
      if (beaker.overlap(container_group)) {
        lab.add.tween(beaker).to({ x: 0, y: 0 }, 1000, "Quart.easeOut", true);
        beaker.scale.setTo(.25);
        if(beaker_tween) beaker_tween.pause(); 
      }
    });


    //lab coat
    //labCoat = addImage(1170, 525, "lab_coat", true, "draggable", true, 1);
   // var beakertagged_c = addImage(1235, 131, "aparatus_container", false, "", false, 1);
    var beakertagged = addImage(900, 300, "beakertagged", true, "draggable", true, 1);
    beakertagged.visible = false;
    beaker.events.onDragStop.add(function() {
      if (!beaker.overlap(container_group)) {
        lab.add
          .tween(beakertagged)
          .to({ x: 900, y: 300 }, 1000, "Sine.easeOut", true);
        setTimeout(function() {
          beakertagged.visible = true;
          beaker.visible = false;
          isbeakertagged = true;
          beakertagged.scale.setTo(.6);
        }, 1000);
     
      } else {
beaker.x = 1170;
beaker.y = 525;
           }

      
    });

  

beakertagged.events.onDragStop.add(function() {
      if (beakertagged.overlap(container_group)) {
        lab.add.tween(beakertagged).to({ x: 1235, y: 131}, 1000, "Sine.easeOut", true);
        setTimeout(function() {
          beakertagged.visible = false;
          beaker.visible = true;
         isbeaker = true;
          beaker.scale.setTo(.25);
        }, 1000);
     
      } 
});

     
    var burette_c = addImage(1235, 218, "aparatus_container", false, "", true, 1);
    burette = addImage(0, 0, "burette", true, "draggable", true, .15);
    burette_c.addChild(burette);
    container_group.add(burette_c);
    apparatus.push(burette);
    burette.events.onDragStop.add(function() {
      if (burette.overlap(container_group)) {
        lab.add.tween(burette).to({ x: 0, y: 0 }, 1000, "Quart.easeOut", true);
        burette.scale.setTo(.15);
      }
    });

    var burette_tap_c = addImage(1235, 305, "aparatus_container", false, "", true, 1);
    burette_tap = addImage(0, 0, "burette_tap", true, "draggable", true, 1);
    burette_tap_c.addChild(burette_tap);
    container_group.add(burette_tap_c);
    apparatus.push(burette_tap);
    burette_tap.events.onDragStop.add(function() {
      if (burette_tap.overlap(container_group)) {
        lab.add.tween(burette_tap).to({ x: 0, y: 0 }, 1000, "Quart.easeOut", true);
        burette_tap.scale.setTo(1);
      }
    });

    var conical_flask_c = addImage(1235, 392, "aparatus_container", false, "", true, 1);
    conical_flask = addImage(0, 0, "conical_flask", true, "draggable", true, .3);
    conical_flask_c.addChild(conical_flask);
    container_group.add(conical_flask_c);
    apparatus.push(conical_flask);
    conical_flask.name = 'conical_flask';
    conical_flask.events.onDragStop.add(function() {
      if (conical_flask.overlap(container_group)) {
        lab.add.tween(conical_flask).to({ x: 0, y: 0 }, 1000, "Quart.easeOut", true);
        conical_flask.scale.setTo(.3);
        if(conical_flask_tween) conical_flask_tween.pause();
      }
    });

    //toggle conical_flask
    var conical_flasktagged = addImage(600, 200, "conical_flasktagged", true, "draggable", true, 1);
    conical_flasktagged.visible = false;
    conical_flask.events.onDragStop.add(function() {
      if (!conical_flask.overlap(container_group)) {
        lab.add
          .tween(conical_flasktagged)
          .to({ x: 600, y: 200 }, 1000, "Sine.easeOut", true);
        setTimeout(function() {
          conical_flasktagged.visible = true;
          conical_flask.visible = false;
          isconical_flasktagged = true;
          conical_flasktagged.scale.setTo(.6);
        }, 1000);
     
      } else {
conical_flask.x = 1170;
conical_flask.y = 525;
           } 

    });

    var hcl_cork_c = addImage(1235, 479, "aparatus_container", false, "", true, 1);
    hcl_cork = addImage(0, -100, "hcl_cork", true, "draggable", true, 1);
    HCl2 = addImage(0, 0, "HCl2", true, "draggable", true, .3);
    HCl2.addChild(hcl_cork);
    hcl_cork_c.addChild(HCl2);
    container_group.add(hcl_cork_c);
    apparatus.push(HCl2);
    HCl2.events.onDragStop.add(function() {
      if (HCl2.overlap(container_group)) {
        lab.add.tween(HCl2).to({ x: 0, y: 0 }, 1000, "Quart.easeOut", true);
        HCl2.scale.setTo(.3);
      }
    });

    var graduated_cylinder_c = addImage(1235, 566, "aparatus_container", false, "", true, 1);
    graduated_cylinder = addImage(0, 0, "graduated_cylinder", true, "draggable", true, .2);
    graduated_cylinder_c.addChild(graduated_cylinder);
    container_group.add(graduated_cylinder_c);
    apparatus.push(graduated_cylinder);
    graduated_cylinder.events.onDragStop.add(function() {
      if (graduated_cylinder.overlap(container_group)) {
        lab.add.tween(graduated_cylinder).to({ x: 0, y: 0 }, 1000, "Quart.easeOut", true);
        graduated_cylinder.scale.setTo(.2);
      }
    });

    var NaOh2_c = addImage(1235, 653, "aparatus_container", false, "", true, 1);
    NaOH_cork = addImage(0, -100, "NaOH_cork", true, "draggable", true, 1);
    NaOh2 = addImage(0, 0, "NaOh2", true, "draggable", true, .3);
    NaOh2.addChild(NaOH_cork);
    NaOh2_c.addChild(NaOh2);
    container_group.add(NaOh2_c);
    apparatus.push(NaOh2);
    NaOh2.events.onDragStop.add(function() {
      if (NaOh2.overlap(container_group)) {
        lab.add.tween(NaOh2).to({ x: 0, y: 0 }, 1000, "Quart.easeOut", true);
        NaOh2.scale.setTo(.3);
      }
    });

    var phenolphthalein_bottle_c = addImage(1148, 653, "aparatus_container", false, "", true, 1);
    phenolphthalein_dipper = addImage(0, -50, "phenolphthalein_dipper", true, "draggable", true, 1);
    phenolphthalein_bottle = addImage(0, 10, "phenolphthalein_bottle", true, "draggable", true, .35);
    phenolphthalein_bottle.addChild(phenolphthalein_dipper);
    phenolphthalein_bottle_c.addChild(phenolphthalein_bottle);
    container_group.add(phenolphthalein_bottle_c);
    apparatus.push(phenolphthalein_bottle);
    phenolphthalein_bottle.events.onDragStop.add(function() {
      if (phenolphthalein_bottle.overlap(container_group)) {
        lab.add.tween(phenolphthalein_bottle).to({ x: 0, y: 0 }, 1000, "Quart.easeOut", true);
        phenolphthalein_bottle.scale.setTo(.35);
      }
    });

    var pipette_c = addImage(1148, 566, "aparatus_container", false, "", true, 1);
    pipette = addImage(0, 0, "pipette", true, "draggable", true, .2);
    pipette_c.addChild(pipette);
    container_group.add(pipette_c);
    apparatus.push(pipette);
    pipette.events.onDragStop.add(function() {
      if (pipette.overlap(container_group)) {
        lab.add.tween(pipette).to({ x: 0, y: 0 }, 1000, "Quart.easeOut", true);
        pipette.scale.setTo(.2);
      }
    });

    var retot_stand_and_cork_c = addImage(1148, 479, "aparatus_container", false, "", true, 1);
    retot_stand_and_cork = addImage(0, 0, "retot_stand_and_cork", true, "draggable", true, .1);
    retot_stand_and_cork_c.addChild(retot_stand_and_cork);
    container_group.add(retot_stand_and_cork_c);
    apparatus.push(retot_stand_and_cork);
    retot_stand_and_cork.events.onDragStop.add(function() {
      if (retot_stand_and_cork.overlap(container_group)) {
        lab.add.tween(retot_stand_and_cork).to({ x: 0, y: 0 }, 1000, "Quart.easeOut", true);
        retot_stand_and_cork.scale.setTo(.1);
      }
    });

    container_group.visible = false;
 
  },
  update: function() {
    apparatus.forEach( function (item) {
      if(item && item.input.isDragged){

        if(!item.overlap(container_group)){
            if(beaker_tween) beaker_tween.pause();
            if(conical_flask_tween) conical_flask_tween.pause();
            item.scale.setTo(.6);
            
        }

      }
    })
 }


};




//typer functions
function nextLine(title, num, name) {
 
  line = title[num].trim().split(' ');
  wordIndex = 0;
  lab.time.events.repeat(wordDelay, line.length, function () {
    nextWord(num, name);
  }, this);
  lineIndex++;
}
function nextWord(num, name) {
  content.text = content.text.concat(line[wordIndex] + " ");
  speakSound('typer');
  wordIndex++;
  if (wordIndex === line.length) {
    setTimeout(function () {
      nextButton.visible = true;
      if(name === 'preparation'){
        if(num === 0){
          //reveal lab clothes
          labCoat.visible = true;
          goggles.visible = true;
          gloves.visible = true;
        } 
        //reveal aparatus
        if(num === 1){
          container_group.visible = true;
        }
        // pulsate beaker 
        if(num === 2) {
          beaker_tween = lab.add.tween(beaker.scale).to({x: .35, y: .35}, 300, Phaser.Easing.Linear.None, true, 0, -1, true)
          beaker.events.onDragStop.add(function() {
            if (beaker.overlap(container_group)) {
              beaker_tween = lab.add.tween(beaker.scale).to({x: .35, y: .35}, 300, Phaser.Easing.Linear.None, true, 0, -1, true)
            }
          });
        }

        // pulsate flask
        if(num === 3) {
          conical_flask_tween = lab.add.tween(conical_flask.scale).to({x: .35, y: .35}, 300, Phaser.Easing.Linear.None, true, 0, -1, true)
          conical_flask.events.onDragStop.add(function() {
            if (conical_flask.overlap(container_group)) {
              conical_flask_tween = lab.add.tween(conical_flask.scale).to({x: .35, y: .35}, 300, Phaser.Easing.Linear.None, true, 0, -1, true)
            }
          });
        }


      }
    },500)
  }
}
//toggling beaker


function executeStep(steps) {
  switch (steps.case) {

    // moving from one instruction step to the other.
    case 'steps':
        if(steps.instructions.steps.length > steps.num + 1){
          if(steps.instructions.title === 'preparation'){
            if(steps.num === 0 && (!isCoat || !isGloves || !isGoggles)){
              return alert('lab attire not probably worn');
            } 
            if(steps.num === 2 && beaker.overlap(container_group)){
              return alert('label your beaker clearly (base)');
            }
          }
          if(nextButton){
            nextButton.destroy();
          }
          nextButton = addImage(40, 70,"nextButton",true,"clickable", false, .6);
          nextButton.case = 'steps'
          nextButton.instructions = steps.instructions;
          nextButton.num = steps.num + 1;
          nextButton.events.onInputDown.add( executeStep, this);
          content.text = '';
          nextButton.visible = false;
          instructionCard.addChild(nextButton);
          nextLine(steps.instructions.steps, steps.num + 1, steps.instructions.title);
          
        }else{
          
        }
      break;
  
    default:
      break;
  }

 
