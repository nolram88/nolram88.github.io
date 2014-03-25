/*
Copyright (c) 2013 lonely-pixel.com, Suffick at codepen.io (http://codepen.io/suffick)

Originally posted at http://codepen.io/stuffit/pen/fhjvk

View this and others at http://lonely-pixel.com

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.
*/


var two_PI = Math.PI * 2;

window.requestAnimFrame =
    window.requestAnimationFrame ||
    window.webkitRequestAnimationFrame ||
    window.mozRequestAnimationFrame ||
    window.oRequestAnimationFrame ||
    window.msRequestAnimationFrame ||
    function(callback) {
        window.setTimeout(callback, 1000 / 60);
    };

//-------------------------------------------------

var Vector = function(x, y) {

    this.x = x || 0;
    this.y = y || 0;
}

Vector.prototype.sub = function(v) {

    if (v.x != null && v.y != null) {

        this.x -= v.x;
        this.y -= v.y;

    } else {

        this.x -= v;
        this.y -= v;
    }

    return this;
};

Vector.prototype.add = function(v) {

    if (v.x != null && v.y != null) {

        this.x += v.x;
        this.y += v.y;

    } else {

        this.x += v;
        this.y += v;
    }

    return this;
};

Vector.prototype.mul = function(v) {

    if (v.x != null && v.y != null) {

        this.x *= v.x;
        this.y *= v.y;

    } else {

        this.x *= v;
        this.y *= v;
    }

    return this;
};

Vector.prototype.div = function(v) {

    if (v.x != null && v.y != null) {

        this.x /= v.x;
        this.y /= v.y;

    } else {

        this.x /= v;
        this.y /= v;
    }

    return this;
};

Vector.prototype.normalize = function() {

    var length = this.length();

    if (length > 0) {
        this.x /= length;
        this.y /= length;
    }

    return this;
};

Vector.prototype.length = function() {

    return Math.sqrt(this.x * this.x + this.y * this.y);
};

Vector.prototype.distance = function(v) {

    var x = this.x - v.x;
    var y = this.y - v.y;

    return Math.sqrt(x * x + y * y);
};

Vector.prototype.reset = function() {

    this.x = 0;
    this.y = 0;

    return this;
};

Vector.prototype.neg = function() {

    this.x *= -1;
    this.y *= -1;

    return this;
};

Vector.add = function(v1, v2) {

    if (v2.x != null && v2.y != null) {

        return new Vector(
        v1.x + v2.x,
        v1.y + v2.y);

    } else {

        return new Vector(
        v1.x + v2,
        v1.y + v2);
    }
};

Vector.sub = function(v1, v2) {

    if (v2.x != null && v2.y != null) {

        return new Vector(
        v1.x - v2.x,
        v1.y - v2.y);

    } else {

        return new Vector(
        v1.x - v2,
        v1.y - v2);
    }
};

Vector.mul = function(v1, v2) {

    if (v2.x != null && v2.y != null) {

        return new Vector(
        v1.x * v2.x,
        v1.y * v2.y);

    } else {

        return new Vector(
        v1.x * v2,
        v1.y * v2);
    }
};

Vector.div = function(v1, v2) {

    if (v2.x != null && v2.y != null) {

        return new Vector(
        v1.x / v2.x,
        v1.y / v2.y);

    } else {

        return new Vector(
        v1.x / v2,
        v1.y / v2);
    }
};

//-------------------------------------------------

var Point = function(x, y, fixed){

    this.pos = new Vector(x, y);
    this.pre = new Vector(x, y);
    this.acc = new Vector();

    this.fixed = fixed;
};

Point.prototype.move = function(v) {

    if(this.fixed) return;

    this.pos.add(v);
};

Point.prototype.add_force = function(v) {

    if(this.fixed) return;

    this.acc.add(v);
};

Point.prototype.update = function(delta) {

    if(this.fixed) return;

    delta *= delta;

    var x = this.pos.x,
        y = this.pos.y;

    this.acc.mul(delta);

    this.pos.x += x - this.pre.x + this.acc.x;
    this.pos.y += y - this.pre.y + this.acc.y;

    this.acc.reset();
  
    this.pre.x = x;
    this.pre.y = y;
};

Point.prototype.check_walls = function(x, y, w, h) {

    this.pos.x = Math.max(x + 1, Math.min(w - 1, this.pos.x));
    this.pos.y = Math.max(y + 1, Math.min(h - 1, this.pos.y));

    if (this.pos.y >= h - 1)
        this.pos.x -= (this.pos.x - this.pre.x + this.acc.x);
};

Point.prototype.draw = function(ctx, size) {

    if(this.fixed) {

        ctx.fillStyle = 'rgba(255,0,0,0.2)';
        ctx.beginPath();
        ctx.arc(this.pos.x, this.pos.y, size * 3, 0, two_PI, false);
        ctx.fill();
    }

    ctx.fillStyle = (this.fixed) ? '#EDEA26' : '#aaa';
    ctx.beginPath();
    ctx.arc(this.pos.x, this.pos.y, size, 0, two_PI, false);
    ctx.fill();
};

//-------------------------------------------------

var Constraint = function(p1, p2) {

    this.p1 = p1;
    this.p2 = p2;
    this.length = p1.pos.distance(p2.pos);
    this.stretch = this.length * 0.15;
}

Constraint.prototype.resolve = function() {

    var dists  = Vector.sub(this.p2.pos, this.p1.pos),
        length = dists.length(),
        diff   = length - this.length;

    dists.normalize();

    var f = dists.mul(diff * 0.5);

    this.p1.move(f);
    this.p2.move(f.neg());
};

Constraint.prototype.draw = function(ctx, stress) {

    if(stress) {

        var diff = this.length - this.p1.pos.distance(this.p2.pos);

        var per = Math.round(Math.min(Math.abs(diff / this.stretch), 1) * 255);

        ctx.strokeStyle = 'rgba(255, '+(0 + per)+', '+(0 + per)+', 0.8)';

    } else ctx.strokeStyle = 'rgba(255,0,0,0.8)';

    ctx.beginPath();
    ctx.moveTo(this.p1.pos.x, this.p1.pos.y);
    ctx.lineTo(this.p2.pos.x, this.p2.pos.y);
    ctx.stroke();
};

//-------------------------------------------------

var JSVerlet = function(canvas, options) {

    this.canvas = canvas;
    this.ctx = canvas.getContext('2d');
    this.ctx.lineWidth = 1;

    this.width = canvas.width;
    this.height = canvas.height;
    this.options = options || {};

    this.constraints = [];
    this.points = [];
    this.draw_points = [];

    this.mouse = new Vector();
    this.gravity = this.options.gravity || new Vector(0, 0.98);
    this.point_size = this.options.point_size || 2;
    this.show_stress = this.options.show_stress;

    this.key = {
        ctrl: false,
        alt: false
    };

    canvas.oncontextmenu = function(e) {

        e.preventDefault();
    };

    var _this = this;

    if (this.options.edit) {

        document.onkeydown = function(e) {

            if (e.keyCode == 17) {

                _this.key.ctrl = true;

            } else if (e.keyCode == 16) {

                _this.draw_points = [];

            } else if (e.keyCode == 18) {

                _this.key.alt = true;
            }
        };

        document.onkeyup = function(e) {

            if (e.keyCode == 17) {

                _this.key.ctrl = false;
                _this.draw_points = [];

            } else if (e.keyCode == 18) {

                _this.key.alt = false;
            }
        };
    }

    if (this.options.edit || this.options.drag) {

        canvas.onmousedown = function(e) {

            var rect = _this.canvas.getBoundingClientRect();
            _this.mouse.x = e.clientX - rect.left;
            _this.mouse.y = e.clientY - rect.top;

            _this.mouse.down = true;

            if (_this.options.edit) {

                if (e.which == 3) {

                    if (_this.get_mouse_point())
                        _this.remove_point(_this.get_mouse_point())

                } else {

                    if (_this.key.ctrl) {

                        var p = _this.get_mouse_point();

                        if (!p) {

                            p = new Point(_this.mouse.x, _this.mouse.y, _this.key.alt);
                            _this.points.push(p);
                        }

                        if (_this.draw_points.length) {

                            _this.constraints.push(
                                new Constraint(
                                    p,
                                    _this.draw_points[_this.draw_points.length - 1]
                                )
                            );
                        }

                        _this.draw_points.push(p);

                    } else if (_this.options.drag)
                        _this.mouse_point = _this.get_mouse_point();
                }

            } else if (_this.options.drag)
                _this.mouse_point = _this.get_mouse_point();
        };

        canvas.onmouseup = function(e) {

            _this.mouse.down = false;
            _this.mouse_point = null;
        };

        canvas.onmousemove = function(e) {

            var rect = _this.canvas.getBoundingClientRect();
            _this.mouse.x = e.clientX - rect.left;
            _this.mouse.y = e.clientY - rect.top;
        };
    }
};

JSVerlet.prototype.draw = function(ctx) {

    ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);

    var i = this.constraints.length;
    while(i--) this.constraints[i].draw(ctx, this.show_stress);

    var i = this.points.length;
    while(i--) this.points[i].draw(ctx, this.point_size);

    if(this.mouse_point) {

        ctx.beginPath();
        ctx.arc(this.mouse_point.pos.x, this.mouse_point.pos.y, this.point_size * 3, 0, two_PI);
        ctx.fillStyle = 'rgba(255, 0, 0, 0.2)';
        ctx.fill();

        ctx.beginPath();
        ctx.arc(this.mouse_point.pos.x, this.mouse_point.pos.y, this.point_size, 0, two_PI);
        ctx.fillStyle = (this.mouse_point.fixed) ? '#EDEA26' : '#aaa';
        ctx.fill();
    }

    if(this.draw_points.length) {

        var point = this.draw_points[this.draw_points.length - 1];

        ctx.beginPath();
        ctx.arc(point.pos.x, point.pos.y, this.point_size * 3, 0, two_PI);
        ctx.fillStyle = 'rgba(255, 0, 0, 0.2)';
        ctx.fill();

        ctx.beginPath();
        ctx.arc(point.pos.x, point.pos.y, this.point_size, 0, two_PI);
        ctx.fillStyle = '#aaa';
        ctx.fill();
    }
};

JSVerlet.prototype.update = function(iter) {

    if(this.key.ctrl) return;

    iter = iter || 6;

    var delta = 1 / iter;
    
    var n = iter;
    while(n--) {

        if (this.mouse_point) {

            this.mouse_point.pos.x += (this.mouse.x - this.mouse_point.pos.x)/iter;
            this.mouse_point.pos.y += (this.mouse.y - this.mouse_point.pos.y)/iter;
        }

        var i = this.points.length;
        while(i--) {

            var point = this.points[i];
            point.add_force(this.gravity);
            point.update(delta);
            point.check_walls(0, 0, this.width, this.height);
        }

        var i = this.constraints.length;
        while(i--) this.constraints[i].resolve();
    }
};

JSVerlet.prototype.remove_point = function(point) {

    var i = this.constraints.length;
    while (i--) {

        var constraint = this.constraints[i];
        if (constraint.p1 == point || constraint.p2 == point)
            this.constraints.splice(this.constraints.indexOf(constraint), 1);
    }

    if (this.points.indexOf(point) != -1)
        this.points.splice(this.points.indexOf(point), 1);
};

JSVerlet.prototype.get_mouse_point = function() {

    var closest;

    var i = this.points.length;

    while(i--) {

        var point = this.points[i];

        if(point.pos.distance(this.mouse) < 10) {

            closest = point;
        }
    }

    return closest;
};

JSVerlet.prototype.add_point = function(x, y, fixed) {

    var point = new Point(x, y, fixed);

    this.points.push(point);

    return point;
};

JSVerlet.prototype.add_constraint = function(p1, p2) {

    this.constraints.push(new Constraint(p1, p2));
};

JSVerlet.prototype.add_shape = function(shapes) {

    if(!(shapes instanceof Array)) {

        if(!(shapes instanceof Shape)) {

            console.log('Error: shape is not an instance of Shape.');
            return false;
        }

        this.points = this.points.concat(shapes.points);
        this.constraints = this.constraints.concat(shapes.constraints);

        return true;
    }

    var i = shapes.length;

    while(i--) {

        var shape = shapes[i];

        if(!(shape instanceof Shape)) {

            console.log('Error: shape[' + i + '] is not an instance of Shape.');
            return false;
        }

        this.points = this.points.concat(shape.points);
        this.constraints = this.constraints.concat(shape.constraints);
    }

    return true;
};

//-------------------------------------------------

var Shape = function() {};

//-------------------------------------------------

var Rectangle = function(x, y, w, h) {

    var p1 = new Point(x, y);
    var p2 = new Point(x + w, y);
    var p3 = new Point(x, y + h);
    var p4 = new Point(x + w, y + h);

    var c1 = new Constraint(p1, p2);
    var c2 = new Constraint(p2, p3);
    var c3 = new Constraint(p3, p4);
    var c4 = new Constraint(p4, p1);
    var c5 = new Constraint(p1, p3);
    var c6 = new Constraint(p2, p4);

    this.points = [p1,p2,p3,p4];
    this.constraints = [c1,c2,c3,c4,c5,c6];
};

Rectangle.prototype = new Shape();

//-------------------------------------------------

window.onload = function() {

    var canvas = document.getElementById('c');
    canvas.width = 500;//window.innerWidth;
    canvas.height = 204;//window.innerHeight;
    var ctx = canvas.getContext('2d');

    var jsv = new JSVerlet(canvas, {drag: true, edit: false, show_stress: true});

    // demo scene ------------------------------

        var p1  = jsv.add_point(100, 50, true);//left tip
        var p2  = jsv.add_point(120, 50, false);
        var p3  = jsv.add_point(135, 60, false);
        var p4  = jsv.add_point(150, 70, false);//left dip
        var p5  = jsv.add_point(185, 55, false);
        var p6  = jsv.add_point(190, 40, false);
        var p7  = jsv.add_point(205, 25, true);//left top
        var p8  = jsv.add_point(220, 40, false);
        var p9  = jsv.add_point(225, 55, false);
        var p10 = jsv.add_point(250, 70, true);//top middle
        var p11 = jsv.add_point(275, 55, false);
        var p12 = jsv.add_point(280, 40, false);
        var p13 = jsv.add_point(295, 25, true);//right top
        var p14 = jsv.add_point(310, 40, false);
        var p15 = jsv.add_point(315, 55, false);
        var p16 = jsv.add_point(340, 70, false);//right dip
        var p17 = jsv.add_point(355, 60, false);
        var p18 = jsv.add_point(370, 50, false);
        var p19 = jsv.add_point(390, 50, true);//right tip
        var p20 = jsv.add_point(380, 70, false);
        var p21 = jsv.add_point(365, 85, false);
        var p22 = jsv.add_point(345, 105, false);//right pit  
        var p23 = jsv.add_point(325, 135, false);
        var p24 = jsv.add_point(315, 150, false);
        var p25 = jsv.add_point(305, 160, false);
        var p26 = jsv.add_point(280, 180, false);
        var p27 = jsv.add_point(250, 200, false);//bottom middle
        var p28 = jsv.add_point(225, 180, false);
        var p29 = jsv.add_point(205, 160, false);
        var p30 = jsv.add_point(190, 145, false);
        var p31 = jsv.add_point(175, 135, false);
        var p32 = jsv.add_point(145, 105, false);// left pit
        var p33 = jsv.add_point(120, 85, false);
        var p34 = jsv.add_point(105, 70, false);
// inner design
        var  i1 = jsv.add_point(250, 85, false);//center
        var  i2 = jsv.add_point(275, 70, false);//
        var  i3 = jsv.add_point(295, 65, false);//top right
        var  i4 = jsv.add_point(310, 70, false);//
        var  i5 = jsv.add_point(325, 85, false);//
        var  i6 = jsv.add_point(325, 95, false);//
        var  i7 = jsv.add_point(310, 110, false);//
        var  i8 = jsv.add_point(295, 130, false);//
        var  i9 = jsv.add_point(275, 155, false);//
        var  i10 = jsv.add_point(250, 170, false);//bottom
        var  i11 = jsv.add_point(225, 155, false);//
        var  i12 = jsv.add_point(205, 130, false);//
        var  i13 = jsv.add_point(190, 110, false);//
        var  i14 = jsv.add_point(175, 95, false);//
        var  i15 = jsv.add_point(175, 85, false);//
        var  i16 = jsv.add_point(190, 70, false);//
        var  i17 = jsv.add_point(205, 65, false);//top left
        var  i18 = jsv.add_point(225, 70, false);//

    jsv.add_constraint(p1, p2);
    jsv.add_constraint(p2, p3);
    jsv.add_constraint(p3, p4);
    jsv.add_constraint(p4, p5);
    jsv.add_constraint(p5, p6);
    jsv.add_constraint(p6, p7);
    jsv.add_constraint(p7, p8);
    jsv.add_constraint(p8, p9);
    jsv.add_constraint(p9, p10);
    jsv.add_constraint(p10, p11);
    jsv.add_constraint(p11, p12);
    jsv.add_constraint(p12, p13);
    jsv.add_constraint(p13, p14);
    jsv.add_constraint(p14, p15);
    jsv.add_constraint(p15, p16);
    jsv.add_constraint(p16, p17);
    jsv.add_constraint(p17, p18);
    jsv.add_constraint(p18, p19);
    jsv.add_constraint(p19, p20);
    jsv.add_constraint(p20, p21);
    jsv.add_constraint(p21, p22);
    jsv.add_constraint(p22, p23);
    jsv.add_constraint(p23, p24);
    jsv.add_constraint(p24, p25);
    jsv.add_constraint(p25, p26);
    jsv.add_constraint(p26, p27);
    jsv.add_constraint(p27, p28);
    jsv.add_constraint(p28, p29);
    jsv.add_constraint(p29, p30);
    jsv.add_constraint(p30, p31);
    jsv.add_constraint(p31, p32);
    jsv.add_constraint(p32, p33);
    jsv.add_constraint(p33, p34);
    jsv.add_constraint(p34, p1);

    //Inner design
     jsv.add_constraint(i1, i2);
    jsv.add_constraint(i2, i3);
    jsv.add_constraint(i3, i4);
    jsv.add_constraint(i4, i5);
    jsv.add_constraint(i5, i6);
    jsv.add_constraint(i6, i7);
    jsv.add_constraint(i7, i8);
    jsv.add_constraint(i8, i9);
    jsv.add_constraint(i9, i10);
    jsv.add_constraint(i10, i11);
    jsv.add_constraint(i11, i12);
    jsv.add_constraint(i12, i13);
    jsv.add_constraint(i13, i14);
    jsv.add_constraint(i14, i15);
    jsv.add_constraint(i15, i16);
    jsv.add_constraint(i16, i17);
    jsv.add_constraint(i17, i18);
    jsv.add_constraint(i18, i1);
//connect inner and outer

    jsv.add_constraint(i15, p4);
    jsv.add_constraint(i15, p32);
    jsv.add_constraint(i16, p5);
    jsv.add_constraint(i17, p7);
    jsv.add_constraint(i18, p9);
    jsv.add_constraint(i1, p10);
    jsv.add_constraint(i2, p11);
 
    jsv.add_constraint(i3, p13);
    jsv.add_constraint(i4, p15);
    jsv.add_constraint(i5, p16);
    jsv.add_constraint(i5, p22);
    jsv.add_constraint(i7, p23);
    jsv.add_constraint(i8, p25);
    jsv.add_constraint(i9, p26);
    jsv.add_constraint(i10, p27);
    jsv.add_constraint(i11, p28);
    jsv.add_constraint(i12, p29);
    jsv.add_constraint(i13, p31);
 


   // jsv.add_shape(new Rectangle(500, 70, 70, 70));
  
  //  var square = new Rectangle(630, 70, 50, 50);

  //  square.points[1].fixed = true;

  //  jsv.add_shape(square);

    //----------------------------------------

    var Loop = function() {

        //var time = new Date().getTime();

        jsv.update(16);
        jsv.draw(ctx);

        //console.log(new Date().getTime() - time);

        requestAnimFrame(Loop);
    };

    Loop();
};
