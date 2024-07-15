# NetworkReceiptPrinter

This is an library that allows you to print to a network receipt printer using Node network sockets.

## What does this library do?

In order to print a receipt on a receipt printer you need to build the receipt and encode it as in the ESC/POS or StarPRNT language. You can use the [`ThermalPrinterEncoder`](https://github.com/NielsLeenheer/ThermalPrinterEncoder) library for this. You end up with an array of raw bytes that needs to be send to the printer. One way to do that is using a network connection.

## Configuration

When you create the `NetworkReceiptPrinter` object you can specify a number of options to help with the library with connecting to the device. 

### Network settings

When a network printer is connected to the network it will get assigned a IP address. Consult the manual of your printer for a method to get the network settings of your printer. Once you have the IP address of your printer, provide them as properties when you instantiate the `NetworkReceiptPrinter` object:

- `host`: The IP address of the printer.
- `port`: The port number of the printer, by default this is `9100`.

For example, to connect to `192.168.1.133` using the default port:

    const receiptPrinter = new NetworkReceiptPrinter({ 
        host:   '192.168.1.133'
    });


## Connect to a receipt printer

The actually connect to the printer you need to call the `connect()` function. To find out when a receipt printer is connected you can listen for the `connected` event using the `addEventListener()` function.

    receiptPrinter.addEventListener('connected', device => {
        console.log(`Connected to printer`);
    });

The callback of the `connected` event is passed an object with the following properties:

-   `type`<br>
    Type of the connection that is used, in this case it is always `network`.


## Commands

Once connected you can use the following command to print receipts.

### Printing receipts

When you want to print a receipt, you can call the `print()` function with an array, or a typed array with bytes. The data must be properly encoded for the printer. 

For example:

    /* Encode the receipt */

    let encoder = new ThermalPrinterEncoder({
        language:  'esc-pos',
        codepageMapping: 'epson'
    });

    let data = encoder
        .initialize()
        .text('The quick brown fox jumps over the lazy dog')
        .newline()
        .qrcode('https://nielsleenheer.com')
        .encode();

    /* Print the receipt */

    receiptPrinter.print(data);


### Disconnect from the printer 

When you are done with printing, you can call the `disconnect()` function. This will terminate the connection to the printer. If the connection to the printer is closed, the library will emit a `disconnected` event.

    receiptPrinter.addEventListener('disconnected', () => {
        console.log(`Disconnected from the printer`);
    });

    receiptPrinter.disconnect();

## License

MIT