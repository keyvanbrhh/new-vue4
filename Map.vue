<template>
    <div class="main">
        <div id="map"
             :lat="lat"
             :lng="lng"
             :zoom="zoom"
             :markerLatLng="markerLatLng"
             :markerName="markerName"
        >





        </div>
        <div class="map-logo"></div>
    </div>
</template>

<script>
    import L from 'leaflet'
import  'leaflet.markercluster'

    export default {
        name: 'mapirMap',
        props: {
            lat: {
                type: Number,
                default: 33.1375
            },
            lng: {
                type: Number,
                default: 56.2170
            },
            zoom: {
                type: Number,
                default: 6
            },
            markerLatLng: {
                type: Array,
                default: [[33.1375, 56.2170]]
            },
            markerName: {
                type: Array,
                default: ['CHAVOSH','cafemobile']

            },
            mdraggble: {
                type: Boolean,
                default: true

            }






        },
        data () {
            return {
                map: null,
                tileLayer: null,
                layers: [{
                    id: 0,
                    name: 'markers',
                    active: true,
                    features: ''
                }]
            }
        },
        created () {
        },
        components: {
        },
        computed: {
        },
        mounted () {
            this.initMap()
            this.initLayers()


        },
        methods: {
            initMap: function () {
                this.map = L.map('map').setView([this.lat, this.lng], this.zoom)

                this.tileLayer = L.tileLayer.wms('http://map.ir/shiveh', {
                    layers: 'Shiveh:ShivehGSLD256',
                    format: 'image/png'
                })

                this.tileLayer.addTo(this.map)
                this.map.attributionControl.setPrefix(false)


            },
            initLayers () {

                var markers = [];
                if (this.markerLatLng.length > 0) {
                    for (var i = 0; i < this.markerLatLng.length; i++) {
                        markers[i] = {
                            id: i,
                            name: this.markerName[i],
                            type: 'marker',
                            coords: this.markerLatLng[i],

                        }
                    }
                    this.layers[0].features = markers
                }
                var myIcon = L.icon({
                    iconUrl: 'http://www.pngall.com/wp-content/uploads/2017/05/Map-Marker-Free-Download-PNG.png',
                    iconSize: [32, 32],
                    popupAnchor: [-3, -76]
                })


//draggble marker with custom marker
                var markerss=L.marker([33.1375, 56.2170],{draggable:true ,icon:myIcon}).addTo(this.map)
                //update  positon of marker
                markerss.on("dragend", function (ev) {

                    var chagedPos = ev.target.getLatLng();
                    this.bindPopup(chagedPos.toString()).openPopup();
                });


                //add marker on click on map
                this.map.on('click',  (e)=> {




                 var  nmarker =  L.marker([e.latlng.lat,e.latlng.lng], {
                        draggable: true,
                        title: "Resource location",
                        alt: "Resource Location",
                        riseOnHover: true,
                      icon:myIcon
                    }).addTo(this.map)
                        .bindPopup(e.latlng.toString()).openPopup();

                    nmarker.on("dragend", function (ev) {

                        var chagedPos = ev.target.getLatLng();
                        this.bindPopup(chagedPos.toString()).openPopup();

                    });

                })
                var addressPoints = [
                    [-37.8210922667, 175.2209316333, "2"],
                    [-37.8210819833, 175.2213903167, "3"],
                    [-37.8210881833, 175.2215004833, "3A"],
                    [-37.8211946833, 175.2213655333, "1"],
                    [-37.8209458667, 175.2214051333, "5"],
                    [-37.8208292333, 175.2214374833, "7"],
                    [-37.8325816, 175.2238798667, "537"],
                    [-37.8315855167, 175.2279767, "454"],
                    [-37.8096336833, 175.2223743833, "176"],
                    [-37.80970685, 175.2221815833, "178"],
                    [-37.8102146667, 175.2211562833, "190"],
                    [-37.8088037167, 175.2242227, "156"],
                    [-37.8112330167, 175.2193425667, "210"],
                    [-37.8116368667, 175.2193005167, "212"],
                    [-37.80812645, 175.2255449333, "146"],
                    [-37.8080231333, 175.2286383167, "125"],
                    [-37.8089538667, 175.2222222333, "174"],
                    [-37.8080905833, 175.2275400667, "129"]
                ]


                var  nnmarkers = new L.markerClusterGroup();

                for (var i = 0; i < addressPoints.length; i++) {
                    var a = addressPoints[i];
                    var title = a[2];
                    var marker =   L.marker(new L.LatLng(a[0], a[1]), {
                        icon: myIcon,
                        title: title
                    });
                    marker.bindPopup(title);
                    nnmarkers.addLayer(marker);
                }
                this.map.addLayer(nnmarkers);
                this.layers.forEach((layer) => {
                    var markerFeatures = layer.features
                    var myIcon = L.icon({
                        iconUrl: 'http://www.pngall.com/wp-content/uploads/2017/05/Map-Marker-Free-Download-PNG.png',
                        iconSize: [32, 32],
                        popupAnchor: [-3, -76]
                    })
                    markerFeatures.forEach((feature) => {
                        feature.leafletObject = L.marker(feature.coords, {icon: myIcon})
                            .bindPopup(feature.coords.toString() + "   " + feature.name).addTo(this.map)

                    })
                })





























            }





                   // this.map.on('click', onMapClick);

              //  function onMapClick(e) {

             //       var geojsonFeature = {

             //           "type": "Feature",
                 //       "properties": {},
                //        "geometry": {
                  //          "type": "Point",
                   //         "coordinates": [e.latlng.lat, e.latlng.lng]
                 //       }
                  //  }

                 //   var marker;

                 //   L.geoJson(geojsonFeature, {

                  //      pointToLayer: function(feature, latlng){

                    //        marker = L.marker(e.latlng, {

                     //           title: "Resource Location",
                       //         alt: "Resource Location",
                       //         riseOnHover: true,
                          //      draggable: true,

                         //   }).bindPopup("<input type='button' value='Delete this marker' class='marker-delete-button'/>");

                         //   marker.on("popupopen", onPopupOpen);

                         //   return marker;
                        }
                  //  }).addTo(this.map);
               // }







    }
</script>

<style>
@import "~leaflet.markercluster/dist/MarkerCluster.css";
@import "~leaflet.markercluster/dist/MarkerCluster.Default.css";
    @import "~leaflet/dist/leaflet.css";
    .map {
        cursor: url("https://s3-us-west-2.amazonaws.com/s.cdpn.io/9632/happy.png");
    }

    .leaflet-container {
        width: 500px;
        height: 500px;
        cursor: url("https://s3-us-west-2.amazonaws.com/s.cdpn.io/9632/happy.png");

    }
    .main {
        position: absolute;
    }
    .map-logo {
        position: absolute;
        background-image: url("data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAK4AAABACAYAAACDQkwcAAAdkUlEQVR4AezSOQ5CMQxFUWzL+Z9BDCASiYIVsAT2v61wCzfUKEWQn3SK199N7z1NJ32dHyZQGBwFS1inlpZQgsOgEIzasHAFCseKA0644o6Klv5CDTdccMQeWxQYZIZwFY4dzmh4vu3x+vBS37qR10EAxw+dQCeOu/MF55yDvfZ6c845512vg9frbDAvwBu4pKOjBYmKAipGoqGZiidAghoKJJBGQsP8PH9egeLzBl99Hz4IZX/8sPnpL88Hn//x4uqrv17efE+v7uDRyK16bdwAvbG8vVbvrtTopRoT4xdq4lxNDi1nQFOW6QHQjHEKNCvmTtT8MdCCcQS0KJYO1XIfaMU4AFoVaz2g9a7a6ABtGm2gLbHdAtoxmkA2sdsA2jPqQPYa0L5wVJWzAuQS7rLylIC8ReUTfhEoqGAeKCTCOZEFiohoBihmpIHiIpECShpJoJRIJ1QmDpSNAeWMKFBeFCKqGLaEgEqiHFSVAFDV8APVRN2nGoYXqClaHtV2q44LqGvpOX/4u+v47s/u/te/t+1f/NqwffZzabP/bXzZ8+CYnrEifv+/E/PA/oQHe0/4zDLcVca5TV0YO/9LuO+Jp+KZGBFTYiX5dDbwzbPi7W/Ph1/+89H9T/ziHh+9ND5BfmUZ+Vi9Nu6Q31je3qp3N2r0Wo2J8Ss1cakmL5CnjHPkacvMEHnWOEOeE/MDtXCKvGicIC+J5WO1coS8ahwir4n1PvLGgdrsIW8ZXeRtsdNBthlt5F2x10K2G03k/QayQzjrylVDdgtPVXkryL6y8peQAyJYVKECclhE8iKHHBWxLHLcyCAnRDKNnDJSyGmRSapsAjkXR84bMeSCKEZVKYJcNsLIFVENqVoQuW4EkBui6Vctw4fcFh2v6npUz418YOm71KFTHf3b3pmHtVWmDf+EAFlCICyEsEMCIQtZCJCEsBAWAoSwQ1kolAXa2ulUnVHHmXHRujg6avvV2jou+L01dmov69Jx+tpFqtTSFtDR16uOo9Kx1latXbSlSy2E9znmPniIOYS9r07++F1ecjV5OOSX59zP/dzPfTQ/YGvWHBhuUD1/vCbxlncsCTkPJocFEwL/XxHXA24LPnDbECKUzzEKl3/F7to65rNqkMAt7n+KuIhmnKTBsZakQVtLUt/5JmX38Vp5y8a0CD4h8DUTF6RlwCwbiZCleYQaP2AuXj/CWdU3xrl+0C2uW9wfWKLGGbjcrNpyrFZWbxT4sInwYUHFhUGZEJjHItSV9DjLF6zOLWNsJCwHcIvrFpcQF6dVPWhrVfVfbFFsPFyZkASzL22hxCVm2kAIDZLL6aKKr1lLXx5j3zDoFtctLrW4OKpBFCYMjraq3jxRL22qjfVj407Nt7g0iGn9CWm1HgLLcWbXi7i0UxPXLa5bXKBdOfhds/zOrTnRgSDvvIlLR/hATKtGZL/LaNw4xgJp3eK6xZ2muDgXlyRueKM4NgKXdz7EJeJaPkKGSN/olffbUdb1/W5x3eLOWFzgUmvi0/sswnDcs7kUlwaBNA9CBK3Gg1/2FXPZy7i0v3xx3eKizYe3z1Un7z5dlbTjaLlq65FS5ebPypUvfF2l3o42IPZcadDsn424KEzA5d3wl4xwHvg2J+J6INiwuaBCZD/rZfr9GOvGwV+uuG5xL1Rp935sVnW/nC6+/UFV5HJLGK9SwWMVYRhmROQi8gMZnuayCF7DfarwVduyhPd8XC5/7nyD+o2ZiIsz3Cq/e6kkgAnyzlpcL1iQxSHSYmi+ls8YHS/8MsV1i3u2PPW1PVnSu38nCavDMEyHSEFoEEqEAv6bBD/XI9JB5nw6jVbUJgpsezVHeN/ZeuXr0xUXxbgDp5ulTTBZzkpcGsS2IfALG6+jqzpGmTf0/7LEdYuL6hT2v5unfKg6PLAAw7BERDzk6SPhbssnEQY/FyISwI1UkDgXYcoR+NTvNcWvudqiOjANcQdtnYm9R+sTVCDvjMX1gExCFPxi+Vu9SlaPMZG0bnEXXFxbXkH/1bz8AyN5eYfmUtzhMt3rzySLWkDCKJDTseqLQSpPZcHPfaH6T4CIhtdrQOA8RNFdasGqU/WJf5+iuIjEwctt8ichZJixuHSEH4QJBoTpA0ZLt1vchRPXllM4cDLD+Gp/iu6xFxKVt61PkPzmKan01tc1SQ99kmGwXsrPeWs24p4q0W79oyQiH4QNhImKQdQVADSUVcBQVgFDGQVn9dZMBBdeHwmZJy0iB1FUE81rOVore2Gq4qJZt/90i7QBxpiRuF7wjZIgMhBFXzGWbXeLuzDinsnIfc0qU96g9/Uz4esLhAFms2xEPqLoppio5YfTdd0zEfezwpQNTZHBuGAhIJ73uKi1egxlFOzg0o6LCzSm2GlKJotMLrqKhzjYiCiS+DGrPqmSPD8VcXG+75Bvfb86jg/vPW1xvRHBEPNkIYrPMX+1xy3u/It7Ii3n+TZBeCHMXmKECBFHiim1ILCJ5+lZ+opGcb+tOKd/quIeMSU/lsTj4O8dhGARxd8oq4ChrAI2DXHtLNaQy1xZiCBih5WQ18Dn1J1skG93LS6iK7H/2yXSFvhCTFtcBnx7lDC4+RLz171ucedX3OP6nO7q4BAt3HaDId70AwIQoSCFCpEOs2/xZrXsLluxsd+VuP8uSFmn9OVI4L2YuBwoo4DhzEJcO80ax0IsISKFCBu6xIEdl1uU+6Yg7uCVDnn3HnNMwEzFDYE/UI5b3PkX96jOuLEoIEgJE4YP3PU8EXTAC2Y0HiICZuQ0WAwVb1JK77hUlNVLJS6S9jG9P5eQlvGDtFUGbA7FRSQ5yhuH0MLvaH4uK+oOW7uy35W4tq7EA583irPhvWYnLgoVdrvFnR9xP001rtVxeVLyTIigoawCNpaLyCvAUDqMfFTKF+RVQihXiDAfzdG/4Ezcf+enrkvh+STADO79g7SVBmwq4uK/B8FUxB1rGZeXBV9CKbHAl/GY1cfqpC+6Ehfn7BLJLa1invesQwW0OHvFLe7ci3tYk/WAmMURT5gJjYXYWA4OWVycfLIUITDrZgR6eZn70jTrrhZn9zmKO5SXulbly3GQNh2jEpe04PJEeMNYbCI1Bj/3mExcHLhLcOALpoYJsPiZjIg/jLQpDroS90qH7IU3LDH8WS/O3OmwuRf3H+rMuwXeDOEEqbKLMCpxSSt4f9gk0AR4eeUfMiQ/5mRxhqTVrhFzWOIJ71+RjlGJ63j4ldh0iGB7R3WIghMeVEfI1qVECY0hXC6RNptEXBopOxVPzLrBTM+y4/XSlyYRF5D3H66Nw9+INqt02BYvs3sDYo7EHTWUHOpXpd8KOdQfpcoqwijEdZQ2GpHE9/bO7U9D0jpJhw3latfKueyJ0pZnYBTikg8L+BGHX1MDONqd2QmrTlYmbbpSl9IzUp+yf6QhuffCIs2zx6uUtX/RRfNAXpK4wBI1ud4lHJFEhJ07C2P/jMQdcCHu4MmWhEX4e8xkA0JEfFM66YmdI8wbDs5OXLe4I/qSvv2K9FXwYfr+KG0xRiGuo7QxuAThDEb+YFrqemd53KEc3f8jwgOytBTiOjuWpbxOFFLxeanaOllZ44W6pMfeK5EK8dc7ExcHxg9AJEAmpHClLHDp962KPlfinmuT/gF3caZbvimIvDCaT/kRRvtmt7gzF/eqztK7V57WAVulPuO320wz5lxcamnf0Ws3ONs5Q9KuUfk6LMTKMjEqcUkxcxBMVJpV8YKGs5UpO6ZSj/t9Y9Jf3y4U42EljUJcDwQXfnctoiCAQS873STf4UrcSx2yDfjfaFZFNojiJ73y3WWNMxQXNQN5a6dU3wxxI3s88Z9hxqjEdSKtJorJLHg3DUnrpFbhE6PuETmXMzE8KM3EqMR1kDYOkYpm2kZc2ukUkl+oV+MzoweFuDQYIwzChVyE+aPqhE2uxL3SKduKX8dMyhp5cEF6hEntEVyLzpptm564bnGvaC09rySk1v9E2vQSjEJccswZQCzEJBxO4WFD2tPOimw+ydY9ImSz4iZKm4VRietM2qVCfuOZcpB2GuKONCXtOFwmi8SvyVHcsdbxcIFPXuzjca4rcUe6ZK+BuDMuJFfCNmPRGq/sm91Hd6Yu7sUUy+7NcckVTqWlEJeIOUFaIUJj8OOZP01P3+SkOgxJq38kgcOOnyCtJQujEpd8apuQdlVcaNOZMiTtDI7ujDZpek/UKIyTiOtFxLnEYv/V/Jj7pyDuHvz3nPXRHWIHpJ/RsMG1uG5xv0su2b4hRln0E2kNFoxKXCfbphpzYFDF5xmZW5zV436SpX/4J9KWZGMuxPVE+MJMnhzDZhSeKUvdMdMzZ7bFmv5vFimb8LGdiQvj+ZPTYlMRd7RL/gaIO/vDkghTPI1XdZTZ8YJbXGpxv1YXW28LE2dCPpw1vhuWZsGoxHWQVoRIrgsR1JzIyNrmrJD8Xxn6h5xK61pcbxhDgjD4etGLrdr4O65W6w7McMbtO1atqHAhLm864sKMuwv/e8zyeDrsgEDIkO0RUX+c1fWSW9yJ4o4mV/R/mJj3kMmPr4T48Udp9aUYhbjOYs6UVkFY3clM43ZnJyA+Sk97MJLJFE2Q1mzEpiwuzICkO2nxE5rY34/U6A7OIMZ9832L1DAdcV8riHnAlbjfd8q2w4w7q4YgPGKRQJSrGekRDUdY7Zvt4rrF/VZt2b5NpOuEW3zgeAWWrgzDpXUmLvx9CWmDiZjzhsjo5rOZuTucHd3BpRWyHBZixUZsGuLSIXQRwKyrg9V+8QZN7K1Xa7V90xH3XJ3qGXSg0pdKXNL5RTGIW3jAEve4K3Evd8g2z0bciStciLuImlAxzb/qILPucdT07sB/qrjnVWU7ByQ5q2v8w/VwZ+KN1x1oyzEKcR3rWINhRkq9I0bUfi4rb7ezM2cfGQwPitgsEYwB0uZg0xSXBrMgB1KeCQgtIe8TybG/x+Wdirhotu3tKYjHX8d0kVUIhIKbTETRF/XSF12JO9wuXYe/dq6a3hHpGRX5jNED3uk3HGW3/0e1GT2jKNu+Lz579U38eDPEpKSSRJCWQlyStGx4nRihfVgkue5iVv5eZ4clP0xLux/lcYUTpC3KwaYjLqTDKMcn5H1cE/O7q4tS+yYTd7Qx+cA/SqRLYaHnSZXHJRVtKRDZBj67drglsWcKW76/AXHntM1oBHyDUonZV+LhX7mWkXXTEXbr5pFfYGNnm2LRwHl55Z7D4sKNT0cmLyv1DTXCBx4Ot0LmeOYgtQKjEpdKmifi5atQR/K3nZzyRdIa7hd4e0+UtjAXm6G4CIMreYvuUUSs+qJMvc1WnzpAFtfWmDLwdbVqszU9tpJF9+ATd5dJds7Y4IsGkfuYPvwWVCF2yJW4n9THl+NfiHlt7IzQg8B5HJpXcb2nuHkLq+jO9zkNTx/ntr/0LXfprvO+y3qGcfyW2uHhdPUM+wMBnXYCgaAOO8EIfrudkDY7gtae4VCcJT3DYUB4S89wBE5zz3AkImqxneimnuEYnMae4VicBjvC+p5hEU5dz3AcIn5Rz7C4tuc84lxCze7TCVU7jsWXbvkfUdHG16Iz71wdktiuYfnnetJoSfAhR8ItkDMuU3IlhrIKGJW4VLJsSlDefDWzsM/ZKd8P9U6kNeVhVOLCGONQb/k6lZc4M5aFyI9ie1vuTAy7rjdf8vBhc+KT+02SP63RRDbHcRlKCDPYLmoViLoXIThi+kdF/JOuqsNGO+X7dpqjE/HXz0srfXI1EUIBF52GyACRcxF5sR6+5nR6aHm2Z3hltmeYc7wcCf0RbyoEldkMCpgEIRNhOcK3w7aTwQouUzP9i3l072xYTGhhtlDAdYYjAsjCImi4tJOJS8qN+5Bjy1dkybeNZhUddHY8/UNd+gPk8ICQlkJcUmkiPOmI/PtRF9k4nhmLhWtNRRhIpMIkJQTJORAr05yIS17YE+nUrHohr/l8c2KPK3HPt0q7703l46/zWKiHl0RCvCeBi0+CDz3lZ4IG0n6JCAmIGg2y8uE62ROESKrCxjQIanHJdypfeC8ZmrnT3kjU3WvLNPc766twWIvPtIyJ0hbkYxTiOpYm8hEhT6rixV8W6gqIEMaZuCR5PUiTUQhcdxxCDP+NgkwEj3zQcpKyRjqpWCsZkdtbIlo7lRMQnzWIVxALvvkQlyywJwzEIR3u4yNCYUYO/5kQBh8OHxEA18IFWeEEAAirrsFQVgFzJS5JKH/4EJUhXoysQ6r0tbYM84CzvgrHDcYnHaXFT0BQieuQ+YlByCoEgYZvi9KetZVl7jtr1tcRork4AeH46C8e/N48+H8mUdXm4gQEefNKikhflhC45NISxVuuxL3aId/bWxqbBuNcswf0MRHsnxEsBNPZQ+hQVgFDWQVsTIlQ1WCuxCXfgslbuCZesOVjjfFZqr4KXxlyuuv4AsVEaQswKnGdpSuzAvyKjxXoNhGHJW3lGX1I3gZCXldnzhw+S0/y38HFmTNn5QKpsVxv8yfVkuemcsoXNQVZnylg+8N48y8uyuNiKA32I9xf48CF/HxAWQUMZRWwMRlOHTYmRyQiaacurrNFbBwi+cFo6dJTWtPfqPoqfJmW071EEKYiby7A0R1n4jqTNlnL45b8O1f7vOPxdFzeMyW6RYS883TKl+aw26pCZPcUiR6aSl8FdML30DuVoioiTLiG4iJ8cVZiY34A71d2/HFWYGMBQOB1doKW2wleZoePCFlqR9BlJ7QTGwvD6cDGwoGIdmwsEqcNG4tCRLfaiVmCjcXitGBjQoSo2U7cYmwsHqcJGxMjEhoxXNpZiOs4y/oTacN0bkDuPnn6n66iExBUfRW+1Oc82yoIT8IlJKSFoztOxXVSRZac7u9nOZqr30LVEMRWkd53ukQ3PvPOobiOGYpQor5le17s6tFWVf9UxD27RNpdFePLh1kem19x3eI6xvm+sLgRIdTdQvWKr5NNL03WV+GEPveZRcECFfm0LxzdcRSXUtrcQP+yz5G0rlow2SrT+74pSW0iYtW5EJcUG/sgBBDX6v+7QLjaBtK6Ene0I7GvpyS2iEixzZO4bnFhhnHMrAgQsSwPuvKucMmiIVV+94i2tG+yvgrHdbndqVw/2URpCzEKcZ1KawzglR7NAWldN71D8hoOnrJoV94li+QRs+9MxCWnRsnNSlDFmeENU9y9tlZ1/1Sb3h1vlNwPi2GvOX5An1tcJ30HOBAShCGESWye9p5wad2/FPkbR1LLDk6lIciZtALrrZFCGcjvoq+C8/DgM6N+80z64w6X69b1ZsvlcC3jC7DJxHVYuDluRqmWi4NqjlTLNk2nP+7FVtn2uzR8MVGYNAtx3eLCB+QoKouUJuJD3lq8ii8yvSrS33hUUfhfoynlh6bbgulihsnao9QKKcR1zLMGkqX9PMewZTYdya9W6Xd+aUm5cXeWVEFK+0ELKARkFgBic4MNIVEwpPikN0r55X3F4gcvNKr2Tqcj+dX2xN4dhdEl8HelL/Rj/z0d0ybOxHVMs5DzolTiTjaGC3GpUnQuIaXxOAguiBoEi44oHt1LfG+ovHCnKOP6j2WmDedUZTtm2/Tucobp+V61Lp6IPckQszs5Q1ETwq86ZjRsnatnQFyu0r72dVny6ncLEssfUUeJifwtCX9Sfj4cIWyKDTD8NSO284NS6ZrzDapd030GhK1D0f9ORdwKUhaFNt/i0kiLES7pwnwdd5aQuA69VOHbCn8YeD2T2D4Ecac1hqO4juORNkUCJwPk5EOsGsageURlsoMlvwuWZHRHpFbtFRpv+DiheM0pedm2i8rKHpu6emAu24xezjRt/Zc2wwzXyyF3AAdZEiKYDMNmZeLN3+VlvT4fT90ZrdXtv1SduuNMpebpzyyq1e8Wyle+Vyz/9fvF8usHiqSrUL3Cje+ZZatPVCk3Dderd6LSxr6ZPnXnoxrxHei4egg5RJhPccnbl3xEdBSNK93OLK07xmm/9wxn2aMnOV0r3mHXxxNiEbc5cjx4D0tnPOzbcNM3/u1rT/m3r/6U12RawUyEFeVPxwjzYEte4hUs+iK48YEzIc2PnhQsXnGYXy0mxgBxycKz4QOPQAgfDkgp/DCy/LYTMTXrEOtPxFSvPxGLU7X+hBAhqlx/PK7iccSGE/HlT55MqNz0raRq2wVpzc6r8tq3RxW1B22K2oH5buxsMxYe+i4z76nBFP3iLXJlbmdYeNKS0DD9H2NjLa+qVb89acx82WbK61/A55yh6jCgMcXO7J9zNoCkvYdJp+EucAhp51NcmkPHQLnKIyjzQ1bzWsdC8hHuyp0nfTpNIBG56a/qaXZOxyXe8jccyxpRddidz/rkcB3GkInovoZ/Bi5a51jWOBLWvvNkaFM+fuEgLrnCPwQRj2oCNFv42Su/j1m87+f2DAhbrmngUk5ez+XcvL2j8AyIn/sD+kbblIdQhdhtsD7wJe608y0u+di6ApHew6h+gOrozih35a79nBoZaRWc0uYtXXTBb9leqnrcs4FtXRCoj4+xy998H1U97mh4+64jgkXxcPEI2COHYymrfKVNV2Kae91P3bn24l5pVfT0FAuXObSgosGTJedVXHIHvrQsj/CqU6xlf5/szNlX3I6bYeWpRhh3cCz3T1ZI/n3Q0tdqGaJoYowEOq/sHL9t92SF5GdDm7uIRRjp6HUqHaPl7wopeOjat2Byi4va6W9+whBeAgtcuKvCs3znWVxiNhMQrZlWeKo6h9kreiYT9zS3ay3R4BcvMn+fW//UZOLagpbvv4+t1RFjNDDjWr4P6eybTNxzYS1317JiiTROEEKOyOLQPEveCyt96tqJ6xb3crPyrQOW+Nt0wWwF0WOCCA/g6enzLy6pbaQGkVdJj2v5jnXdrsnEPebT9icotsjyxujmfT5V6yYTdyRo2d7FDDFRx5ub4SWov8zv6J1MXBTn3hLj6ePt2JQazbjmPQLTw25xF17ci4uVPf+slKy7UR5cBGEi6YEpIO0CixtGSMXBvEo/YC7unkTc/r+xSxeR2r4X38lMXTXKW9FPJe7XAUse9cY8hKRO1ubDgbXPUolrC+84MBBcboLZ9scZF8Zb6StZimLc/W5xF0bcb+oUr75jSXjoDpWgknQmD04+QwaoXYkttLjEjCYnjhrX0ONbTrOX7XAm7lFO613BNFYUxKs6RAGX5lX6pk/lGmfiXgjoevlRjkEDXw4J0T2nhBG1+HTwkh3OxEULsz8aGaGB8E0mx7haRB4+6/7/4Izbr8Y0H5h7cd3iolaj+7+sUbzUb0549C/6yKW10bw8CA2jiDN5RDyLCwssqLjkvlPR5P4KpXRh4wCrfsM5zvJdl31+1XuKs3RbL7t6hYYeHAm521CEHEQs4NEYJc9x8m//yq/1lcsBy3rRYcndH/Ma/3wPW6slbUxEwqyb9cMYjOjGgcCKDedDWvdcEbT3nhI0v/hmUMmKZK+gCFLHGHLlPbnZhekWP/nSvrDidUeiKp8fiqq0DkVXWIdicMqtQ7EIYZl1SFRqJ84ClFiH4hFiRAKO2TokQUiLrUMynCJEoXVIjkg02VEUWIeUCFW+HTUiKQ/ItQ5pEMk5dlKM1qFUhDYbkWUd0gF6nEzrUBrCkGEdSsdJtw5lIDINdrLSEHrrUDaOzjpkROQAuVrrUB5OqnUoHyfFTkGydciEo7EOFSKKkuwUA2ZEiRpQ2bEorUOldj4tU2w6XCJHhyYTHn01W3T7o8kR17WJAqsMwZxc+KwSQNgghM+ETaI2FXbtxIWNAYf+CgaQKxtmYS1IGgGSM2AVGQ4/18K/y4bXGeB9YklPnoGjLi7HCEdwSfWbjnlcMXzB0uB1WW5mRCYiAyaeNNIT1lUwuwrhLhlICEvEsjMufpmXnTMQC8SJB4kUCBlcRAgpT0fIzoWfC+HfKeB18aTeBBAHTXkMruMeN3nnjPSc2Tj4A8vczBgp8bRL0hPWQxGBjtvwzoS99uKCvA5lfcEIPukiWCAPjSwT/NwX/h0fXudPPuo9izEc43Hy63kwmwe5mRGBiADiaZekJ6wzidl15sIurLhkORyqruAiqIWnO/x7L+KiZzuGi+o1eA83M8SLfIDUtajzL+7/AlIhtjAS0YciAAAAAElFTkSuQmCC");
        background-repeat: no-repeat;
        background-size: 70px 25px;
        bottom: 0;
        text-align: center;
        width: 70px;
        height: 30px;
        z-index: 1000;
        display: table-footer-group;
        left: 45%;
    }
    </style>
