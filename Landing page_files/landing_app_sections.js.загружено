var landing_app_sections = function($, prefix){
    
    function init_comments(current){
        $(document).ready(function() {
            var owl = $("#comments_slider");
            owl.owlCarousel({
                items: [3],
                itemsDesktop : [1000,2], //5 items between 1000px and 901px
                itemsDesktopSmall : [900,2], //4 items betweem 900px and 601px
                itemsTablet: [600,1], //3 items between 600 and 0;
                itemsMobile : [479,1], //2 itemsMobile disabled - inherit from itemsTablet option
                slideSpeed: 500,
                pagination: false
            });

            var qty_items = $('.l_comment_item').length,
                $comments_nav = $('.comemnts_nav_wrap');

            $(window).resize(function(){
                $comments_nav.removeClass('hidden');
                if(window.outerWidth > 1000 && qty_items <= 3){
                    $comments_nav.addClass('hidden');
                }
                if(window.outerWidth < 1000 && qty_items <= 2){
                    $comments_nav.addClass('hidden');
                }
                if(window.outerWidth < 600 && qty_items == 1){
                    $comments_nav.addClass('hidden');
                }
            }).resize();

            if(current){
                var $block = $('.l_comment_item>div[data-form_data="id='+current+'"]').parent().parent();
                var current_index = $block.parent().find('.owl-item').index($block);
                current_index = current_index > 0 ? current_index - 1 : current_index;
                owl.data('owlCarousel').jumpTo(current_index);
            }

            // Custom Navigation Events
            $("#comments_next").click(function(){
                owl.trigger('owl.next');
            });
            $("#comments_prev").click(function(){
                owl.trigger('owl.prev');
            });
        });  
    }

    function init_gmap(){
        if(typeof gmap_markers === 'undefined'){
            return;
        }
        var le = typeof landing_editor != 'undefined';
        var lat = gmap_markers[0].lat,
            lng = gmap_markers[0].lng,
            zoom = gmap_markers[0].zoom;
        lat = lat == 'undefined' ? 0 : lat;
        lng = lng == 'undefined' ? 0 : lng;
        zoom = zoom && parseInt(zoom) > 0 ? parseInt(zoom) : 12;
        
        var init_google = function(){ 
            var myLatlng = new google.maps.LatLng(lat, lng);
            var mapOptions = {
                center: myLatlng,
                zoom: zoom,
                mapTypeId: google.maps.MapTypeId.ROADMAP
            };
            var map = new google.maps.Map(document.getElementById("gmap"), mapOptions);
            
            var marker = new google.maps.Marker({
                position: myLatlng,
                map: map,
                title: '',
                draggable: le,
                icon: prefix+'images/flag.png'
            });
            if(le){
                // search input
                var input = document.getElementById('pac-input');
                $(input).removeClass('hidden');
                var searchBox = new google.maps.places.SearchBox(input);
                map.controls[google.maps.ControlPosition.TOP_LEFT].push(input);
                searchBox.addListener('places_changed', function () {
                    var location = searchBox.getPlaces()[0].geometry.location;
                    marker.setPosition(location);
                    map.setCenter(location);
                    editor.save('map', 'params[map][lat]='+location.lat()+'&params[map][lng]='+location.lng());
                });
                
                google.maps.event.addListener(marker, 'dragend', function() {
                    var pos = marker.getPosition();
                    editor.save('map', 'params[map][lat]='+pos.lat()+'&params[map][lng]='+pos.lng());
                });
                google.maps.event.addListener(map, 'zoom_changed', function() {
                    zoom = map.getZoom();
                    editor.save('map', 'params[map][zoom]=' + zoom);
                });
            }
        };
        var init_yandex = function(){
            ymaps.ready(function(){
                var controls = ["typeSelector","zoomControl","geolocationControl",
                                "fullscreenControl","rulerControl","routeEditor",
                                "trafficControl"];
                var map = new ymaps.Map("gmap", {
                    center: [lat, lng],
                    zoom: zoom,
                    controls: controls
                });
                var marker = new ymaps.Placemark(
                    [lat, lng], {},
                    {
                        draggable: le
                    }
                );
                map.geoObjects.add(marker);
                if(le){
                    var searchControl = new ymaps.control.SearchControl({
                        options: {
                            float: 'left',
                            floatIndex: 100,
//                            noCentering: true,
                            noPlacemark: true
                        }
                    });
                    map.controls.add(searchControl);
                    searchControl.events.add('resultselect', function (event) {
                        var coords = searchControl.getResultsArray()[event.get('index')].geometry.getCoordinates();
                        marker.geometry.setCoordinates(coords);
                        editor.save('map', 'params[map][lat]='+coords[0]+'&params[map][lng]='+coords[1])
                    }, this);
                    
                    marker.events.add('dragend', function () {
                        var pos = marker.geometry.getCoordinates();
                        editor.save('map', 'params[map][lat]='+pos[0]+'&params[map][lng]='+pos[1]);
                    });
                    map.events.add('boundschange', function () {
                        zoom = map.getZoom();
                        editor.save('map', 'params[map][zoom]=' + zoom);
                    });
                }
            });
        };
        
        switch(gmap_type){
            case 'google':
                if(typeof google != 'undefined'){
                    init_google();
                }else{
                    $.getScript('https://maps.google.com/maps/api/js?key=AIzaSyDF8ZUOnJ4C4trUY3h0-F34lwZeapVJwl8&libraries=places&language='+gmap_lang, init_google);
                }
            break;
            case 'yandex':
                if(typeof ymaps != 'undefined'){
                    init_yandex();
                }else{
                    var map_lng = 'en_US';
                    switch(gmap_lang){
                        case 'ru':
                            map_lng = 'ru_RU';
                        break;
                        case 'uk':
                            map_lng = 'uk_UA';
                        break;
                    }
                    $.getScript('https://api-maps.yandex.ru/2.1/?lang='+map_lng, init_yandex);
                }
            break;
        }
    }
    
    return {
        init_comments: init_comments,
        init_gmap: init_gmap,
        init: function(){
            var hash = window.location.hash;
            if(hash == '#show_privacy'){
                $('#show_privacy').modal('show');
            }
        }
    };
    
}($, prefix);
$(function(){ landing_app_sections.init() });
