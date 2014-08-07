MinervaData
===========

<h3>Interactive Data written in D3</h3>
<strong>Hello there!</strong>
<p>My Name is Sheri Lopez. I am currently and undergraduate studend at the University of New Mexico-Los Alamos. My area of study is mechanical engineering and physics. This respository is being created as part of my summer internship at Fermi National Accelerator Laboratory. I will be creating an interactive database to display the results of the MinerVa experiment. I hope you like it!</p> 


<h4>Interactive graph with blue graph points</h4>


<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">

<title>Antineutrino Quasi-Elastic Scattering vs. Muon Neutrino Quasi-Elastic Scattering</title>

<script type="text/javascript" src="../d3.v3.js"></script>
<style type="text/css">

/*=============================Begin CSS Elements=============================*/

.axis path,
.axis line {
  fill: none;
  stroke: black;
  stroke-width: 2px;
  shape-rendering: crispEdges;
  }

.axis text {
  font-family: sans-serif;
  font-size: 11px;
  fill: black;
  }

#circle:hover{
  fill: #0000FF;
  }

#tooltip{
  position:absolute;
  width:auto;
  height: auto;
  padding: 10px;
  background-color: #CCE5FF;
  -webkit-border-radius: 10px;
  -moz-border-radius: 10px;
  border-radius: 10px;
  -webkit-box-shadow: 4px 4px 10px rgba(0,0,0,0.4);
  -moz-box-shadow: 4px 4px 10px rgba(0,0,0,0.4);
  box-shadow: 4px 4px 10px rgba(0,0,0,0.4);
  pointer-events: none;
  }

#tooltip.hidden{
  display:none;
  }

#tooltip p {
  margin:0;
  font-family:sans-serif;
  position: center;
  font-size: 16px;
  line-height: 20px;
  }

form{
position: absolute;
  left:40px;
  top: 20px;
  }

/*=============================End CSS Elements===============================*/
</style>
</head>
<!--=========================Begin HTML Elements=============================-->
<body>

  <form>
    <label><input type="radio" name="mode" id="dataset1" checked>
      Antineutrino Qusai-Elastic Scattering/Proton</label>

    <label><input type="radio" name="mode" id="dataset2">
      Muon Neutrino Quasi-Elastic Scattering/Neutron</label>
  </form>

  <div id="tooltip" class="hidden">
    <p><strong>Fractional Systematic Uncertainties</strong></p>
    <p><span id="value">100</span>x 10e-39</p>


<!--===========================End HTML Elements=============================-->

<script type="text/javascript">

//=============================Begin JavaScript===============================\\


//----------------------Define Width and Height of Graph----------------------\\

var w = 850;
var h = 600;
var padding = 75;

//------------------------------Dynamic dataset-------------------------------\\

var dataset = [
              [0.0125, 8.13],
              [0.0375, 10.61],
              [0.075, 11.85],
              [0.15, 10.96],
              [0.3, 7.77],
              [0.6, 3.4],
              [1.0, 1.23],
              [1.6, 0.41]
              ];


//--------------------------Create scale functions----------------------------\\

//Create xScale
var xScale = d3.scale.linear()
                .domain([0, 2])
               .range([padding, w - padding * 2]);

//Create yScale
var yScale = d3.scale.linear()
               .domain([0, 20])
               .range([h - padding, padding]);


//-------------------------------Define axis'---------------------------------\\

//Define X axis
var xAxis = d3.svg.axis()
              .scale(xScale)
              .orient("bottom")
              .tickValues([0, 0.5, 1, 1.5, 2])
              .ticks(15);

//Define Y axis
var yAxis = d3.svg.axis()
              .scale(yScale)
              .orient("left")
              .tickValues([0, 2, 4, 6, 8, 10, 12, 14, 16, 18, 20])
              .ticks(20);

//----------------------------Create SVG element------------------------------\\

var svg = d3.select("body")
            .append("svg")
            .attr("width", w)
            .attr("height", h);


//-----------------------------Create Text Labels-----------------------------\\

// text label for the x axis
svg.append("text")
   .attr("x", w / 2.19)
   .attr("y", h - padding / 3)
   .style("text-anchor", "middle")
   .text("GeV{^2}");

//text label for y axis
svg.append("text")
   .attr("transform", "rotate(-90)")
   .attr("y", padding/4)
   .attr("x",0 - (h / 2))
   .attr("dy", "1em")
   .style("text-anchor", "middle")
   .text("cm^2/GeV^2");

//-------Make a Clipping path so elements stay withing the graph space--------\\

//Define clipping path
svg.append("clipPath")
   .attr("id", "chart-area")
   .append("rect")
   .attr("x", padding)
   .attr("y", padding)
   .attr("width", w - padding * 3)
   .attr("height", h - padding * 2);

//------------Chose To Define a Color Gradient for Graphed Points-------------\\

//Define Gradient
svg.append("radialGradient")
   .attr("id", "area-gradient")
   .attr("gradientUnits", "userSpaceOnUse")
   .attr("spreadMethod", "pad")
   .selectAll("stop")
   .data([
        {offset: "10%", color: "#CCE5FF", opacity: "1"},
        {offset: "100%", color: "#0000CC", opacity: "1"}
        ])
   .enter().append("stop")
   .attr("offset", function(d) { return d.offset; })
   .attr("stop-color", function(d) { return d.color; });

//------------------------Create Points for the Graph-------------------------\\

//create circles
svg.append("g")
   .attr("id", "circles")
   .attr("clip-path", "url(#chart-area)")
   .selectAll("circle")
   .data(dataset)
   .enter()
   .append("circle")
   .attr("cx", function(d) {
      return xScale(d[0]);
      })
   .attr("cy", function(d) {
      return yScale(d[1]);
      })
   .attr("r", 6)
   .attr("fill", "url(#area-gradient)")


//-----------Create Tooltip for the Graph(Shows Points on MouseOver)----------\\

//tooltip
.on("mouseover", function(d) {

//Get this bar's x/y values, then augment for the tooltip
    var xPosition = parseFloat(d3.select(this).attr("x")) + xScale.range / 2;
    var yPosition = parseFloat(d3.select(this).attr("y")) / 2 + h / 2;

//Update the tooltip position and value
    d3.select("#tooltip")
      .style("left", xPosition + "px")
      .style("top", yPosition + "px")
      .select("#value")
      .text(d);

//Show the tooltip
    d3.select("#tooltip").classed("hidden", false);
    })
    .on("mouseout", function() {

//Hide the tooltip
    d3.select("#tooltip").classed("hidden", true);

});


//-------------------------Create the x and y Axis----------------------------\\

//Create X axis
svg.append("g")
   .attr("class", "x axis")
   .attr("transform", "translate(0," + (h - padding ) + ")")
   .call(xAxis);

//Create Y axis
svg.append("g")
   .attr("class", "y axis")
   .attr("transform", "translate(" + padding  + ",0)")
   .call(yAxis);

//=============Transition between two datasets (To another Graph)=============\\

//adding an update for new value
d3.select("body")
  .on("click", function() {

//-------------------New Values Our Graph Will Transition To------------------\\

//New values for dataset
dataset2 =[
        [0.0125, 7.61],
        [0.0375, 11.46],
        [0.075, 13.43],
        [0.15, 14.90],
        [0.3, 10.63],
        [0.6, 5.83],
        [1.0, 2.42],
        [1.6, 0.97]
        ];

//-----------------Update scale domains(May or may not need)------------------\\

/*
xScale.domain([0, d3.max(dataset, function(d) { return d[0]; })]);
yScale.domain([0, d3.max(dataset, function(d) { return d[1]; })]);
*/


//---------------------Transition to Circles for the New Graph----------------\\

//Update all circles
svg.selectAll("circle")
   .data(dataset2)
   .transition()
   .duration(1000)
   .ease("linear")
   .each("start", function(d){
           d3.select(this)
              .attr("fill", "blue")
             .attr("r", 10)
        })
   .attr("cx", function(d) {
           return xScale(d[0]);
        })
   .attr("cy", function(d) {
           return yScale(d[1]);
        })
   .transition()
   .duration(1000)
   .attr("fill", "url(#area-gradient)")
   .attr("r", 4);



//---------------------------Update Axis' For New Graph-----------------------\\

//update xAxis
svg.select(".x.axis")
   .transition()
   .duration(1000)
   .call(xAxis);

//Update Y axis
svg.select(".y.axis")
   .transition()
   .duration(1000)
   .call(yAxis);

//------------------------Update Axis Text for new Graph----------------------\\

//Update yAxis Text
/*
svg.select("text")
  .attr("transform", "rotate(-90)")
  .attr("y", padding/4)
  .attr("x",0 - (h / 2))
  .attr("dy", "1em")
  .style("text-anchor", "middle")
  .text("cm^2/GeV^2/Neutron");

//Update xAxis Text
svg.select("text")
   .attr("x", w / 2.19)
   .attr("y", h - padding / 3)
   .style("text-anchor", "middle")
   .text("Gev^2");
*/

//--------------------------end update for new Value--------------------------\\

//Here
});

//=============================End of JavaScript==============================\\

</script>
</body>
</html>


<h4>Error Bar data (Neutrino)</h4>


<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">

<title>Antineutrino Quasi-Elastic Scattering vs. Muon Neutrino Quasi-Elastic Scattering</title>

<style type="text/css">

/*svg {
  font: 12px sans-serif;
}*/

.axis path,
.axis line {
  fill: none;
  stroke: #000;
  shape-rendering: crispEdges;
}

.axis text {
  font-family: sans-serif;
  font-size: 11px;
  fill: black;
  }

path {
  stroke-width: 1.5px;
  stroke: blue;
}

form{
position: absolute;
  left:40px;
  top: 20px;
  }

</style>
<body>

  <form>
    <label><input type="radio" name="mode" id="dataset1" checked>
      Antineutrino Qusai-Elastic Scattering</label>
  </form>

<script src="../d3.v3.js"></script>
<script src="errorbar.js"></script>
<script>

var margin = {top: 100, right: 150, bottom: 100, left: 40},
    size = 5,
    width = 960 - margin.left - margin.right,
    height = 500 - margin.top - margin.bottom,
    padding = -250;

var x = d3.scale
          .linear()
          .range([0, width]),
    y = d3.scale
          .linear()
          .range([height, 0]);


var xAxis = d3.svg.axis()
              .scale(x)
              .orient("bottom")
              /*.tickValues([0, 0.5, 1, 1.5, 2])
              .ticks(15),*/
    yAxis = d3.svg.axis()
              .scale(y)
              .orient("left")
              /*.tickValues([0, 2, 4, 6, 8, 10, 12, 14, 16, 18, 20])
              .ticks(20);*/

var eb = errorBar()
          .oldXScale(x)
          .xScale(x)
          .oldYScale(y)
          .yScale(y)
          .yValue(function(d){return d.y})
          .xValue(function(d){return d.x})
          .xError(function(d){return null})
          .yError(function(d){return d.s});


var svg = d3.select("body").append("svg")
    .attr("width", width + margin.left + margin.right)
    .attr("height", height + margin.top + margin.bottom)
    .append("g")
    .attr("class","container")
    .attr("transform", "translate(" + margin.bottom + "," + margin.top + ")");

d3.json("error.data.json", function(error,data) {

  x.domain([0,d3.max(data.map(function(d) { return d.x; }))]);
  y.domain([0,d3.max(data.map(function(d) { return d.y; }))]);

var d = svg.selectAll("g")
        .data(data),

    dEnter = d.enter()
              .append("g")
              .attr("transform", function(d) {return "translate("+ x(d.x) +","+ y(d.y) +")"})
              .attr("fill","blue");

dEnter.append("circle")
          .attr("r",3);
dEnter.call(eb);

svg.append("g")
      .attr("class","x axis")
      .attr("transform","translate(0," + y.range()[0] + ")")
      .call(xAxis);

svg.append("g")
      .attr("class","y axis")
      .attr("transform","translate(" + x.range()[0] + ",0)")
      .call(yAxis);


// text label for the x axis
svg.append("text")
   .attr("x", width / 2)
   .attr("y", height - padding / 4)
   .style("text-anchor", "middle")
   .text("Gev^2");

//text label for y axis
svg.append("text")
   .attr("transform", "rotate(-90)")
   .attr("y", padding/4)
   .attr("x",0 - (height / 2))
   .attr("dy", "1em")
   .style("text-anchor", "middle")
   .text("cm^2/GeV^2/Proton");


});




</script>
</body>

</html>

<h4>Second graph with error bars (AntiNeutrino)</h4>

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">

<title>Antineutrino Quasi-Elastic Scattering vs. Muon Neutrino Quasi-Elastic Scattering</title>

<style type="text/css">

/*svg {
  font: 12px sans-serif;
}*/

.axis path,
.axis line {
  fill: none;
  stroke: #000;
  shape-rendering: crispEdges;
}

.axis text {
  font-family: sans-serif;
  font-size: 11px;
  fill: black;
  }

path {
  stroke-width: 1.5px;
  stroke: blue;
}

form{
position: absolute;
  left:40px;
  top: 20px;
  }

</style>
<body>
  <form>

    <label><input type="radio" name="mode" id="dataset2">
      Muon Neutrino Quasi-Elastic Scattering</label>
  </form>

<script src="../d3.v3.js"></script>
<script src="errorbar.js"></script>
<script>

var margin = {top: 100, right: 150, bottom: 100, left: 40},
    size = 5,
    width = 960 - margin.left - margin.right,
    height = 500 - margin.top - margin.bottom,
    padding = -250;

var x = d3.scale
          .linear()
          .range([0, width]),
    y = d3.scale
          .linear()
          .range([height, 0]);


var xAxis = d3.svg.axis()
              .scale(x)
              .orient("bottom")
              /*.tickValues([0, 0.5, 1, 1.5, 2])
              .ticks(15),*/
    yAxis = d3.svg.axis()
              .scale(y)
              .orient("left")
              /*.tickValues([0, 2, 4, 6, 8, 10, 12, 14, 16, 18, 20])
              .ticks(20);*/

var eb = errorBar()
          .oldXScale(x)
          .xScale(x)
          .oldYScale(y)
          .yScale(y)
          .yValue(function(d){return d.y})
          .xValue(function(d){return d.x})
          .xError(function(d){return null})
          .yError(function(d){return d.s});


var svg = d3.select("body").append("svg")
    .attr("width", width + margin.left + margin.right)
    .attr("height", height + margin.top + margin.bottom)
    .append("g")
    .attr("class","container")
    .attr("transform", "translate(" + margin.bottom + "," + margin.top + ")");

d3.json("error.data2.json", function(error,data) {

  x.domain([0,d3.max(data.map(function(d) { return d.x; }))]);
  y.domain([0,d3.max(data.map(function(d) { return d.y; }))]);

var d = svg.selectAll("g")
        .data(data),

    dEnter = d.enter()
              .append("g")
              .attr("transform", function(d) {return "translate("+ x(d.x) +","+ y(d.y) +")"})
              .attr("fill","blue");

dEnter.append("circle")
          .attr("r",3);
dEnter.call(eb);

svg.append("g")
      .attr("class","x axis")
      .attr("transform","translate(0," + y.range()[0] + ")")
      .call(xAxis);

svg.append("g")
      .attr("class","y axis")
      .attr("transform","translate(" + x.range()[0] + ",0)")
      .call(yAxis);


// text label for the x axis
svg.append("text")
   .attr("x", width / 2)
   .attr("y", height - padding / 4)
   .style("text-anchor", "middle")
   .text("Gev^2");

//text label for y axis
svg.append("text")
   .attr("transform", "rotate(-90)")
   .attr("y", padding/4)
   .attr("x",0 - (height / 2))
   .attr("dy", "1em")
   .style("text-anchor", "middle")
   .text("cm^2/GeV^2/Neutron");


});





</script>
</body>

</html>



<h4>the data files.jSon and csv for the error bars</h4>

function errorBar(){
    //TODO
    //- Think of an additional way to represent error bars, maybe just the orthognal case (i.e. use width not height)
    //- Does ordinal scale even make sense here?  May need to remove.
    var size = 5,
    xError = function(d) {return exists(d[0].s);},
    yError = function(d) {return exists(d[1].s);},
    errormarker = null, //points will inherit from g.dataset
    xValue = function(d) {return (d[0].x === undefined)?d[0]:d[0].x;},
    yValue = function(d) {return (d[1].x === undefined)?d[1]:d[1].x;},
    oldXScale, //probably can give these smarter defaults
    oldYScale,
    xScale,
    yScale;

    function marks(datapoints){
        datapoints.each(function(d,i) {
            var g = d3.select(this),
            //Use color/errormarker defined.
            errormarker = errormarker || exists(g.datum().errormarker);

            //Error
            var err = g.selectAll(".err")
                .data(function(d){return (!(xError(d) === null) || !(yError(d) === null))?[d]:[]},xValue),
            errEnter,
            errExit,
            errUpdate;


            switch (errormarker) {
            case null: {
                errEnter = err.enter().append("path").attr("class","err")
                errExit = d3.transition(err.exit()).remove(),
                errUpdate = d3.transition(err),
                errTransform = function(selection,a,b){
                    selection.attr("d", function(d){
                        var h = (yError(d) === null)?[-size,size]:[(b(yValue(d) - yError(d)) - b(yValue(d))),(b(yValue(d) + yError(d)) - b(yValue(d)))],
                        w = (xError(d) === null)?[-size,size]:[(a(xValue(d) - xError(d)) - a(xValue(d))),(a(xValue(d) + xError(d)) - a(xValue(d)))];

                        return "M 0," + h[0] +
                            " L 0," + h[1] +
                            " M " + w[0] + "," + h[1] +
                            " L " + w[1] + "," + h[1] +
                            " M " + w[0] + "," + h[0] +
                            " L " + w[1] + "," + h[0]
                    })};;
                break;
            }
            case errormarker: {  //Normalize custom errors to 1px in their definition. Will be scaled back up here by size.
                err.append("use").attr("class",marker + " err");
                err.selectAll("use").attr("xlink:href","#" + marker)
                    .attr("transform",function(d){return "scale(" + xError(d) + "," + yError(d) + ")"});
                break;
            }
            };

            // For quantitative scales:
            // - enter new ticks from the old scale
            // - exit old ticks to the new scale
            if (xScale.ticks) {
                errEnter.call(errTransform, oldXScale, oldYScale);
                errUpdate.call(errTransform, xScale, yScale);
                errExit.call(errTransform, xScale, yScale);
            }

            // For ordinal scales:
            // - any entering ticks are undefined in the old scale
            // - any exiting ticks are undefined in the new scale
            // Therefore, we only need to transition updating ticks.
            else {
                var dx = xScale.rangeBand() / 2, x = function(d) { return xScale(d) + dx; };
                var dy = yScale.rangeBand() / 2, y = function(d) { return yScale(d) + dy; };
                errEnter.call(errTransform, x, y);
                errUpdate.call(errTransform, x, y);
            }
        });
    }

    marks.size = function(_) {
        if (!arguments.length) return size;
        size = _;
        return marks;
    };

    marks.xError = function(_) {
        if (!arguments.length) return xError;
        xError = _;
        return marks;
    };

    marks.yError = function(_) {
        if (!arguments.length) return yError;
        yError = _;
        return marks;
    };

    marks.oldXScale = function(_) {
        if (!arguments.length) return oldXScale;
        oldXScale = _;
        return marks;
    };

    marks.oldYScale = function(_) {
        if (!arguments.length) return oldYScale;
        oldYScale = _;
        return marks;
    };

    marks.xScale = function(_) {
        if (!arguments.length) return xScale;
        xScale = _;
        return marks;
    };

    marks.yScale = function(_) {
        if (!arguments.length) return yScale;
        yScale = _;
        return marks;
    };

     marks.xValue = function(_) {
        if (!arguments.length) return xValue;
        xValue = _;
        return marks;
    };

    marks.yValue = function(_) {
        if (!arguments.length) return yValue;
        yValue = _;
        return marks;
    };

    marks.errormarker = function(_) {
        if (!arguments.length) return errormarker;
        errormarker = _;
        return marks;
    };

    return marks;
};

function exists(a){
    return (a === undefined)?null:a;
};

<h4>Error Data .js (Neutrino) and .js(Antineutrino)</h4>

<h5>.js</h5>

[
{"x":0.0125, "y":7.61,  "s": 1.031},
{"x":0.0375, "y":11.46, "s": 1.448},
{"x":0.075,  "y":13.43, "s": 1.597},
{"x":0.15,   "y":14.90, "s": 1.723},
{"x":0.3,    "y":10.63, "s": 1.215},
{"x":0.6,    "y":5.83,  "s":0.7513},
{"x":1.0,    "y":2.42,  "s":0.5482},
{"x":1.6,    "y":0.97,  "s":0.2530}
];

<h5>.js2</h5>

[
{"x":0.0125, "y":7.61,  "s": 1.031},
{"x":0.0375, "y":11.46, "s": 1.448},
{"x":0.075,  "y":13.43, "s": 1.597},
{"x":0.15,   "y":14.90, "s": 1.723},
{"x":0.3,    "y":10.63, "s": 1.215},
{"x":0.6,    "y":5.83,  "s":0.7513},
{"x":1.0,    "y":2.42,  "s":0.5482},
{"x":1.6,    "y":0.97,  "s":0.2530}
];



