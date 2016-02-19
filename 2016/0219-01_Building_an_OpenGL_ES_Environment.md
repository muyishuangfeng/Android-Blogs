#Displaying Graphics with OpenGL ES

The Android framework provides plenty of standard tools for creating attractive, functional graphical user interfaces. However, if you want more control of what your application draws on screen, or are venturing into three dimensional graphics, you need to use a different tool. The OpenGL ES APIs provided by the Android framework offers a set of tools for displaying high-end, animated graphics that are limited only by your imagination and can also benefit from the acceleration of graphics processing units (GPUs) provided on many Android devices. 

借助GPU加速，openGL能做很多事情。

This class walks you through the basics of developing applications that use OpenGL, including setup, drawing objects, moving drawn elements and responding to touch input.

本课程教你关于OpenGL的基础应用。

The example code in this class uses the OpenGL ES 2.0 APIs, which is the recommended API version to use with current Android devices. For more information about versions of OpenGL ES, see the OpenGL developer guide.

本课程使用的是OpenGL ES 2.0。

>Note: Be careful not to mix OpenGL ES 1.x API calls with OpenGL ES 2.0 methods! The two APIs are not interchangeable and trying to use them together only results in frustration and sadness.

>注意：1.0和2.0的方法不是可互换的

#Building an OpenGL ES Environment

In order to draw graphics with OpenGL ES in your Android application, you must create a view container for them. One of the more straight-forward ways to do this is to implement both a GLSurfaceView and a GLSurfaceView.Renderer. A GLSurfaceView is a view container for graphics drawn with OpenGL and GLSurfaceView.Renderer controls what is drawn within that view. For more information about these classes, see the OpenGL ES developer guide.

GLSurfaceView is just one way to incorporate OpenGL ES graphics into your application. For a full-screen or near-full screen graphics view, it is a reasonable choice. Developers who want to incorporate OpenGL ES graphics in a small portion of their layouts should take a look at TextureView. For real, do-it-yourself developers, it is also possible to build up an OpenGL ES view using SurfaceView, but this requires writing quite a bit of additional code.

GLSurfaceView用于满屏显示或近乎满屏显示；TextureView用于小控件显示；SurfaceView支持更自由的变化，但是更麻烦一点。

This lesson explains how to complete a minimal implementation of GLSurfaceView and GLSurfaceView.Renderer in a simple application activity.

本课程使用GLSurfaceView和GLSurfaceView.Renderer

##Declare OpenGL ES Use in the Manifest

In order for your application to use the OpenGL ES 2.0 API, you must add the following declaration to your manifest:

    <uses-feature android:glEsVersion="0x00020000" android:required="true" />

If your application uses texture compression, you must also declare which compression formats your app supports, so that it is only installed on compatible devices.

    <supports-gl-texture android:name="GL_OES_compressed_ETC1_RGB8_texture" />
    <supports-gl-texture android:name="GL_OES_compressed_paletted_texture" />

For more information about texture compression formats, see the OpenGL developer guide.

##Create an Activity for OpenGL ES Graphics

Android applications that use OpenGL ES have activities just like any other application that has a user interface. The main difference from other applications is what you put in the layout for your activity. While in many applications you might use TextView, Button and ListView, in an app that uses OpenGL ES, you can also add a GLSurfaceView.

The following code example shows a minimal implementation of an activity that uses a GLSurfaceView as its primary view:

    public class OpenGLES20Activity extends Activity {

    private GLSurfaceView mGLView;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        // Create a GLSurfaceView instance and set it
        // as the ContentView for this Activity.
        mGLView = new MyGLSurfaceView(this);
        setContentView(mGLView);
    }
    }

>Note: OpenGL ES 2.0 requires Android 2.2 (API Level 8) or higher, so make sure your Android project targets that API or higher.

##Build a GLSurfaceView Object

A GLSurfaceView is a specialized view where you can draw OpenGL ES graphics. It does not do much by itself. The actual drawing of objects is controlled in the GLSurfaceView.Renderer that you set on this view. In fact, the code for this object is so thin, you may be tempted to skip extending it and just create an unmodified GLSurfaceView instance, but don’t do that. You need to extend this class in order to capture touch events, which is covered in the Responding to Touch Events lesson.

The essential code for a GLSurfaceView is minimal, so for a quick implementation, it is common to just create an inner class in the activity that uses it:

    class MyGLSurfaceView extends GLSurfaceView {

    private final MyGLRenderer mRenderer;

    public MyGLSurfaceView(Context context){
        super(context);

        // Create an OpenGL ES 2.0 context
        setEGLContextClientVersion(2);

        mRenderer = new MyGLRenderer();

        // Set the Renderer for drawing on the GLSurfaceView
        setRenderer(mRenderer);
    }
    }

One other optional addition to your GLSurfaceView implementation is to set the render mode to only draw the view when there is a change to your drawing data using the GLSurfaceView.RENDERMODE_WHEN_DIRTY setting:

    // Render the view only when there is a change in the drawing data
    setRenderMode(GLSurfaceView.RENDERMODE_WHEN_DIRTY);

This setting prevents the GLSurfaceView frame from being redrawn until you call requestRender(), which is more efficient for this sample app.

防止GLSurfaceView被重画

##Build a Renderer Class

The implementation of the GLSurfaceView.Renderer class, or renderer, within an application that uses OpenGL ES is where things start to get interesting. This class controls what gets drawn on the GLSurfaceView with which it is associated. There are three methods in a renderer that are called by the Android system in order to figure out what and how to draw on a GLSurfaceView:

- onSurfaceCreated() - Called once to set up the view's OpenGL ES environment.
- onDrawFrame() - Called for each redraw of the view.
- onSurfaceChanged() - Called if the geometry of the view changes, for example when the device's screen orientation changes.

Here is a very basic implementation of an OpenGL ES renderer, that does nothing more than draw a black background in the GLSurfaceView:

    public class MyGLRenderer implements GLSurfaceView.Renderer {

    public void onSurfaceCreated(GL10 unused, EGLConfig config) {
        // Set the background frame color
        GLES20.glClearColor(0.0f, 0.0f, 0.0f, 1.0f);
    }

    public void onDrawFrame(GL10 unused) {
        // Redraw background color
        GLES20.glClear(GLES20.GL_COLOR_BUFFER_BIT);
    }

    public void onSurfaceChanged(GL10 unused, int width, int height) {
        GLES20.glViewport(0, 0, width, height);
    }
    }

That’s all there is to it! The code examples above create a simple Android application that displays a black screen using OpenGL. While this code does not do anything very interesting, by creating these classes, you have laid the foundation you need to start drawing graphic elements with OpenGL.

>Note: You may wonder why these methods have a GL10 parameter, when you are using the OpengGL ES 2.0 APIs. These method signatures are simply reused for the 2.0 APIs to keep the Android framework code simpler.

If you are familiar with the OpenGL ES APIs, you should now be able to set up a OpenGL ES environment in your app and start drawing graphics. However, if you need a bit more help getting started with OpenGL, head on to the next lessons for a few more hints.