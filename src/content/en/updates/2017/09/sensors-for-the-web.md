project_path: /web/_project.yaml
book_path: /web/updates/_book.yaml
description: Generic Sensor API available for Origin Trials in Chome 62.

{# wf_updated_on: 2017-09-02 #}
{# wf_published_on: 2017-09-02 #}
{# wf_tags: sensors,origintrials,chrome62,news #}
{# wf_blink_components: Blink>Sensor #}
{# wf_featured_image: /web/updates/images/generic/sd-card.png #}
{# wf_featured_snippet: Generic Sensor API available for Origin Trials in Chome 62. #}

# Sensors For The Web! {: .page-title }

{% include "web/_shared/contributors/alexshalamov.html" %}
{% include "web/_shared/contributors/pozdnyakov.html" %}

Today, sensor data is used in many native applications to enable use cases such
as immersive gaming, fitness tracking, augmented or virtual reality. Wouldn't
it be cool to bridge the gap between native and web applications? The [Generic Sensor
API](https://www.w3.org/TR/generic-sensor/), FTW!

## What is Generic Sensor API? {: #what-is-generic-sensor-api }

The [Generic Sensor API](https://www.w3.org/TR/generic-sensor/) is a set of
interfaces which expose sensor devices to the web platform. The API consists
of the base [Sensor](https://w3c.github.io/sensors/#the-sensor-interface)
interface and a set of concrete sensor classes build on top. Having a base
interface simplifies the implementation and specification process for the
concrete sensor classes. For instance take a look at the
[Gyroscope](https://w3c.github.io/gyroscope/#gyroscope-interface)
class, it is really tiny! The core functionality is specified by the base
interface, and Gyroscope merely extends it with three attributes representing
angular velocity.

Typically a concrete sensor class represents an actual sensor on the platform
e.g., accelerometer or gyroscope. However, in some cases, implementation of a
sensor class [fuses](https://w3c.github.io/sensors/#sensor-fusion) data from
several platform sensors and exposes the result in a convenient way to the user.
For example,
[AbsoluteOrientation](https://www.w3.org/TR/orientation-sensor/#absoluteorientationsensor)
sensor provides ready-to-use 4x4 rotation matrix based on the data obtained
from accelerometer, gyroscope and magnetometer.

You might think that the web platform already provides sensor data and you are
absolutely right! For instance, [DeviceMotion](https://www.w3.org/TR/2016/CR-orientation-event-20160818/#devicemotion)
and [DeviceOrientatrion](https://www.w3.org/TR/2016/CR-orientation-event-20160818/#deviceorientation)
events expose motion sensor data, while other experimental APIs provide data
from an environmental sensors. So why do we need new API?

Comparing to the existing interfaces, Generic Sensor API provides great
number of advantages:

- Generic Sensor API is a framework, i.e. it can be easily extended
  with new sensor classes and each of these classes will keep the generic
  interface. The client code initially written for one sensor type can be
  reused for another with very few modifications!
- You can configure the desired sensor data. For example, request sensor reading
  delivery rate suitable for your application needs.
- You can detect whether a sensor is available on the platform.
- Sensor readings have high precision timestamps, enabling better
  synchronization with other activities in your application.
- Sensor data models and coordinate systems are clearly defined, allowing
  browser vendors to implement interoperable solutions.
- The Generic Sensor based interfaces are not bound to the DOM (neither
  navigator nor window objects), and it opens up future opportunities of using
  same API from service workers or implementing Generic Sensor API in headless
  JS runtimes.
- [Security and privacy](#privacy_and_security) aspects are the top priority
  for the Generic Sensor API and provide much better security level compared to
  older sensor APIs. There is integration with Permissions API.

## Generic Sensor APIs in Chrome {: #generic-sensor-api-in-chrome }

At the time of writing, Chrome supports several sensors that you can experiment with.

**Motion sensors:**

- Accelerometer
- Gyroscope
- LinearAccelerationSensor
- AbsoluteOrientationSensor
- RelativeOrientationSensor

**Environmental sensors:**

- AmbientLightSensor
- Magnetometer

You can enable Generic Sensor APIs for development purposes by turning on a
feature flag. Go to [chrome://flags/#enable-generic-sensor](chrome://flags/#enable-generic-sensor)
to enable motion sensors or
[chrome://flags/#enable-generic-sensor-extra-classes](chrome://flags/#enable-generic-sensor-extra-classes)
to enable environmental sensors. Restart Chrome and you should be good to go.

<iframe width="100%" height="320"
  src="https://www.chromestatus.com/feature/5698781827825664?embed"
  style="border: 1px solid #CCC" allowfullscreen>
</iframe>

More information on browser implementation status can be found on
[chromestatus.com](https://www.chromestatus.com/features/5698781827825664?embed)

## Motion sensors are available as an origin trial {: #motion-sensors-origin-trials }

In order to get your valuable feedback, the Generic Sensor API would be
available in Chrome 62 as an [origin trial](https://bit.ly/OriginTrials). You
will need to [request a token](http://bit.ly/OriginTrialSignup), so that the
feature would be automatically enabled for your origin, without the need to
enable chrome flag.

## What are all these sensors? How can I use them? {: #what-are-sensors-how-to-use-them }

Sensors is a quite specific area which might need a brief introduction to the
people who never used them before. If you are familiar with sensors, you can
jump right to the [hands-on coding section](#lets-code). Otherwise, let’s look at each
supported sensor in more detail.

### Accelerometer and Linear acceleration sensor {: #acceleration-and-linear-accelerometer-sensor }

<div class="attempt-right">
  <figure>
    <img src="/web/updates/images/2017/09/sensors/accelerometer.gif"
         alt="Accelerometer sensor measurements">
    <figcaption><b>Figure 1</b>: Accelerometer sensor measurements</figcaption>
  </figure>
</div>

The [Accelerometer](https://www.w3.org/TR/accelerometer/#intro) sensor measures
acceleration of a device hosting the sensor on three axes (X, Y and Z). This
sensor is an inertial sensor, meaning that when the device is in linear free
fall, the total measured acceleration would be 0 m/s<sup>2</sup>, and when a
device lies flat on a table, the acceleration in upwards direction (Z axis)
will be equal to the Earth’s gravity, i.e. g ≈ +9.8 m/s<sup>2</sup> as it is
measuring the force of the table pushing the device upwards. If you push
device to the right, acceleration on X axis would be positive, or negative
if device is accelerated from right toward the left.

<div class="clearfix"></div>

Accelerometers can be used for things like: step counting, motion sensing or
simple device orientation. Quite often, accelerometer measurements are combined
with data from other sources in order to create fusion sensors, such as,
orientation sensors.

The [LinearAccelerationSensor](https://w3c.github.io/accelerometer/#linearaccelerationsensor-interface)
measures acceleration that is applied to the device hosting the sensor,
excluding the contribution of a gravity force. When a device is at rest, for
instance, lies flat on the table, sensor would measure ≈ 0 m/s<sup>2</sup>
acceleration on three axes.

### Gyroscope {: #gyroscope-sensor }

<div class="attempt-right">
  <figure>
    <img src="/web/updates/images/2017/09/sensors/gyroscope.gif"
        alt="Gyroscope sensor measurements">
    <figcaption><b>Figure 2</b>: Gyroscope sensor measurements</figcaption>
  </figure>
</div>

The [Gyroscope](https://w3c.github.io/gyroscope/#intro) sensor measures angular
velocity in rad/s around the device’s local X, Y and Z axis. Most of the
consumer devices have mechanical
([MEMS](https://en.wikipedia.org/wiki/Microelectromechanical_systems))
gyroscopes, which are inertial sensors that measure rotation rate based on
[inertial Coriolis force](https://en.wikipedia.org/wiki/Coriolis_force). A MEMS
gyroscopes are prone to drift that is caused by sensor’s gravitational
sensitivity which deforms sensor’s internal mechanical system. Gyroscopes
oscillate at relative high frequencies e.g., 10’s of kHz, therefore, might
consume more power compared to other sensors.

<div class="clearfix"></div>

### Orientation sensors {: orientation-sensors }

<div class="attempt-right">
  <figure>
    <img src="/web/updates/images/2017/09/sensors/orientation.gif"
         alt="AbsoluteOrientation sensor measurements">
    <figcaption><b>Figure 3</b>: AbsoluteOrientation sensor measurements</figcaption>
  </figure>
</div>

The [AbsoluteOrientationSensor](https://w3c.github.io/orientation-sensor/#orientationsensor-interface)
is a fusion sensor that measures rotation of a device in relation to the
Earth’s coordinate system, while the
[RelativeOrientationSensor](https://w3c.github.io/orientation-sensor/#relativeorientationsensor-interface)
provides data representing rotation of a device hosting motion sensors in
relation to a stationary reference coordinate system.

All modern 3D JavaScript frameworks support quaternions and rotation matrices
to represent rotation, however, if you use WebGL directly,
[OrientationSensor](https://w3c.github.io/orientation-sensor/#orientationsensor-populatematrix)
interface provides convinient methods for WebGL compatible rotation matrices.
Here are few snippets:

<div class="clearfix"></div>

**three.js**

    let torusGeometry = new THREE.TorusGeometry(7, 1.6, 4, 3, 6.3);
    let material = new THREE.MeshBasicMaterial({ color: 0x0071C5 });
    let torus = new THREE.Mesh(torusGeometry, material);
    scene.add(torus);

    // Update mech rotation using quaternion.
    const sensorAbs = new AbsoluteOrientationSensor();
    sensorAbs.onreading = () => torus.quaternion.fromArray(sensorAbs.quaternion);
    sensorAbs.start();

    // Update mech rotation using rotation matrix.
    const sensorRel = new RelativeOrientationSensor();
    let rotationMatrix = new Float32Array(16);
    sensor_rel.onreading = () => {
        sensorRel.populateMatrix(rotationMatrix);
        torus.matrix.fromArray(rotationMatrix);
    }
    sensorRel.start();

**BABYLON**

    const cylinder = new BABYLON.Mesh.CreateCylinder("cylinder", 0.9, 0.3, 0.6, 9, 1 , scene);
    const sensorRel = new RelativeOrientationSensor({frequency: 30});
    sensorRel.onreading = () => cylinder.rotationQuaternion.FromArray(sensorRel.quaternion);
    sensorRel.start();

**WebGL**

    // Initialize sensor and update model matrix when new reading is available.
    let modMatrix = new Float32Array([1,0,0,0, 0,1,0,0, 0,0,1,0, 0,0,0,1]);
    const sensorAbs = new AbsoluteOrientationSensor({frequency: 60});
    sensorAbs.onreading = () => sensorAbs.populateMatrix(modMatrix);
    sensorAbs.start();

    // Somewhere in rendering code, update vertex shader attribute for the model
    gl.uniformMatrix4fv(modMatrixAttr, false, modMatrix);

Orientation sensors enable various use cases, such as, immersive gaming,
augmented and virtual reality.

For more information about motion sensors, advanced use cases and requirements,
please check [motion sensors explainer](https://www.w3.org/TR/motion-sensors/)
document.

## Let’s code! {: #lets-code }

The Generic Sensor API is very simple and easy-to-use! The Sensor interface has
`start()` and `stop()` methods to control sensor state and several event handlers for
receiving notifications about sensor activation, errors and newly available
readings. The concrete sensor classes usually add their specific reading
attributes to the base class.

In this simple example, we use data from an absolute orientation sensor to
modify rotation quaternion of a 3D model. The <code>model</code> is a three.js
<code>Object3D</code> class instance that has <code>quaternion</code> property.

    function initSensor() {
        if ('AbsoluteOrientationSensor' in window) {
            sensor = new AbsoluteOrientationSensor({frequency: 60});
            sensor.onreading = () => model.quaternion.fromArray(sensor.quaternion);
            sensor.onerror = event => {
                if (event.error.name == 'NotReadableError') {
                    console.log("Sensor is not available.");
                }
            }
            sensor.start();
        }
    }

Whenever the device is rotated, model’s rotation quaternion is updated
thus, rotating the model in WebGL scene.

<figure align="center">
  <img src="/web/updates/images/2017/09/sensors/orientation_phone.png"
    alt="Sensor updates 3D model's orientation">
  <figcaption><b>Figure 4</b>: Sensor updates orientation of a 3D model</figcaption>
</figure>

The following extract from [punchmeter demo]
(https://github.com/intel/generic-sensor-demos/tree/master/punchmeter) code
illustrates how linear acceleration sensor can be used to calculate the maximum
velocity of a device assuming that it was initially still.

```
   this.maxSpeed = 0;
   this.vx = 0;

   this.ax = 0;
   this.t = 0;

   function onreading() {
     let dt = (this.accel.timestamp - this.t) * 0.001; // In seconds.
     this.vx += (this.accel.x + this.ax) / 2 * dt;

     let speed = Math.abs(this.vx);

     if (this.maxSpeed < speed)
       this.maxSpeed = speed;

     this.t = this.accel.timestamp;
     this.ax = this.accel.x;
   }
   ....

   this.accel.addEventListener('reading', onreading);

```

The current velocity is calculated as an approximation to integral of the
acceleration function.


<figure align="center">
  <img src="/web/updates/images/2017/09/sensors/punchmeter.png"
    alt="Demo web application for punch speed measurument">
  <figcaption><b>Figure 5</b>: Calculation of punch speed</figcaption>
</figure>


## Privacy and Security {: privacy-and-security }

Sensor readings are sensitive data which can be subject to various attacks from
malicious web pages. Chrome implementation of Generic Sensor APIs embarked on
few limitations to mitigate the possible security and privacy risks. These
limitations must be taken into account by developers who intend to use the API,
so let’s briefly enumerate them.

### Only HTTPS

Because Generic Sensor API is a powerful new feature added to the Web, Chrome
aims to make it available only to secure contexts. In practice it means that
in order to use Generic Sensor API you'll need to access your page through
HTTPS. During the development you can do so via http://localhost but for
production deployment you'll need to have HTTPS set up on your server. See
Security with
[HTTPS article](/web/fundamentals/security/)
for best practices and guidelines there.

### Only main frame

To prevent iframes from reading sensor data the Sensor objects can be created
only within main frame context.

### Sensor readings delivery can be suspended

Sensor readings are only accessible by a visible web page, i.e., when the user
is actually interacting with it. Moreover, sensor data would not be provided
if the user focuses from a main frame to a cross-origin iframe, so that the
main frame cannot infer user input.

## What’s next? {: whats-next }

There is a set of already specified sensor classes to be implemented in nearest
future such as [Proximity sensor](https://w3c.github.io/proximity/) and
[Gravity sensor](https://w3c.github.io/accelerometer/#gravitysensor-interface),
however thanks to the great extensibility of Generic Sensor framework we can
anticipate appearance of even more new classes representing various sensor types.

Another important area for future work is improving of the Generic Sensor API
itself, the Generic Sensor specification is currently a draft which means that
there is still time to make fixes and bring new functionality that developers
need.


## You can help!

The sensor specifications are in active development and we need your feedback to
make sure that this development goes in the right direction. Try the APIs either
by enabling runtime [flags](#generic-sensor-api-in-chrome) in Chrome or taking part
in the [origin trial](#motion-sensors-origin-trials) and share your experience.
Let us know what features would be great to add there for your application'
purposes or if there is something you would modify in the API shape.

Please fill the [survey form](https://docs.google.com/forms/d/e/1FAIpQLSdGKPzubbOaDSgjpre9Pxw6Hr1xwYIwgZEsuUOmbs6JPwvcBQ/viewform)
, also feel free to file [specification issues](https://github.com/w3c/sensors/issues/new)
as well as [bugs](https://bugs.chromium.org/p/chromium/issues/entry)
for the Chrome implementation.


<!--
List features that need attention, feedback.

Make Google form survey with problem desciption, ask to select possible
solutions + free field for proposal.

- Expose sensors to service workers
- Provide sensor readings at higher rates (>60Hz)
- Allow changing sensor options after construction (e.g., frequency)
- Provide solution for synchronization of sensor and screen coordinate systems
- Improve sensor features detection
- Provide better mechanisms for the Sensor interface extensibility
- Clarify whether we need granular permission tokens for orientation sensor
  classes
- Provide new sensor types and/or options (e.g. accuracy)
- Feature policy to enable sensor access for iframes
- Provide yaw, pitch and roll attributes for Orientation sensor?
-->

## Resources

- Generic Sensor API specification: [https://w3c.github.io/sensors/](https://w3c.github.io/sensors/)
- Specification issues: [https://github.com/w3c/sensors/issues](https://github.com/w3c/sensors/issues)
- W3C working group mailing list: [public-device-apis@w3.org](public-device-apis@w3.org )
- Chrome Feature Status: [https://www.chromestatus.com/feature/5698781827825664](https://www.chromestatus.com/feature/5698781827825664 )
- Implementation Bugs: [http://crbug.com?q=component:Blink>Sensor](http://crbug.com?q=component:Blink>Sensor)
- Sensor-dev Google group: [https://groups.google.com/a/chromium.org/forum/#!forum/sensors-dev](https://groups.google.com/a/chromium.org/forum/#!forum/sensors-dev)

{% include "comment-widget.html" %}
