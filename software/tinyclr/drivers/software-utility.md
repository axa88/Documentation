# Software Utility Drivers

---

## Azure SAS

This utility NuGet adds SAS generation used in Azure. See [Microsoft Azure](../tutorials/azure.md) tutorial for more info.

> [!TIP]
> Needed NuGet: GHIElectronics.TinyCLR.Drivers.Azure.SAS

## Basic Graphics

`BasicGraphics` driver is a simpler alternative that runs on all devices, including small devices without native display support. It contains many useful methods for adding simple graphics,including: `SetPixel`, `DrawLine`, `DrawRectangle`, `DrawCircle`, `DrawString`, and `DrawCharacter`. It also includes support for a micro 5px font through `DrawTinyCharacter` and `DrawTinyString`.

There are 2 differences between `DrawCharacter` and `DrawTinyCharacter`, beside the font size! DrawCharacter can accept scaling to enlarge the font, `DrawTinyCharacter` does not. Also, `DrawCharacter` will only set pixels and does not clear blanks. `DrawTinyCharacter` has the same behavior by default but it has a `clear` option to set the blanks to 0 (black).

There are two ways to use BasicGraphics, the first one is agnostic of what display is being used and simply works by calling `SetPixel` method that must be overridden by the user. The user has 2 options, either send the pixel directly to the display or buffer the pixels in a "Video RAM" working buffer and then flush to the screen when desired. Setting the pixels directly on the screen requires zero memory but it is extremely slow.

In the second way, which is the easier way, BasicGraphics will handle allocating the needed video buffer and handles the SetPixel. It already has 2 pixel options, 16BPP 5:6:5 and 1BPP.

> [!TIP]
> Needed NuGet: GHIElectronics.TinyCLR.Drivers.BasicGraphics

An example of the first options, that overrides the `SetPixel` and `Clear` methods. BasicGraphics will not allocate any memory.

```cs
public class BasicGraphicsImp : BasicGraphics{
    public override void SetPixel(int x, int y, uint color){
        // add code to buffer pixels or send directly to display
    }
    public override void Clear(){
       // add optional clear if buffer is used
    }
    // You may need to add this to send an optional buffer...
    public void Flush(){
        // ... for example
        lcd.DrawBufferNative(this.buffer);
    }
}
```

Now, the Basic Graphics driver can be used.

```cs
var basicGfx = new BasicGraphicsImp();
var colorBlue = 0x00FF0000U;
var colorGreen = 0x0000FF00U;
var colorRed = 0x00000FFU;
var colorWhite = 0x00FFFFFFU;

basicGfx.Clear();

basicGfx.DrawString("Hi Insiders!", colorGreen, 15, 15, 2, 1);
basicGfx.DrawString("SITCore", colorBlue, 35, 40, 2, 2);
basicGfx.DrawString("SC13xxx", colorRed, 35, 60, 2, 2);

Random color = new Random();
for (var i = 20; i < 140; i ++)
    basicGfx.DrawCircle((uint)color.Next(), i, 100, 15);

basicGfx.Flush();
```

This example uses the second option where a buffer is allocated automatically. This is the preferred option for using BasicGraphics.

```cs
var basicGfx = new BasicGraphics(160, 128, ColorFormat.Rgb565);
var colorBlue = 0x00FF0000U;
var colorGreen = 0x0000FF00U;
var colorRed = 0x00000FFU;
var colorWhite = 0x00FFFFFFU;

basicGfx.Clear()

basicGfx.DrawString("Hi Insiders!", colorGreen, 15, 15, 2, 1);
basicGfx.DrawString("SITCore", colorBlue, 35, 40, 2, 2);
basicGfx.DrawString("SC13xxx", colorRed, 35, 60, 2, 2);

Random color = new Random();
for (var i = 20; i < 140; i ++)
    basicGfx.DrawCircle((uint)color.Next(), i, 100, 15);

// now send to display, using the specific display driver.
MyDisplaySendBuffer(basicGfx.Buffer);
```

## IR

Infrared remotes are very common and use multiple standards. NCR is very common and a driver is included. A remote with NCR standard will send an 8bit address and an 8bit command with every key press. A repeat signal will be sent as long as the key is continuously pressed.

> [!TIP]
> Needed NuGet: GHIElectronics.TinyCLR.Drivers.Infrared.NecIR

```cs
static void Main() {

    var recievePin = GpioController.GetDefault().OpenPin(SC20260.GpioPin.PH6);
    var ir = new NecIRDecoder(recievePin);

    ir.OnDataReceivedEvent += Ir_OnDataRecievedEvent;            
    ir.OnRepeatEvent += Ir_OnRepeatEvent;
}

private static void Ir_OnDataRecievedEvent(byte address, byte command) {
    // we have a new key press
}
private static void Ir_OnRepeatEvent() {
    // we have repeat
}
```
## QR Code Generator

This driver converts a string to an image, which can be displays on screen for example.

**In the works!**


## GPS Parser

**In the works!**


