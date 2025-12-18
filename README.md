# FileMaker WebDirect eSign Signature Pad

A responsive, premium HTML5 signature pad designed specifically for integration with Claris FileMaker WebDirect.

## Overview

This project provides a ready-to-use HTML signature form that can be embedded into a FileMaker Web Viewer. It leverages the popular [Signature Pad](https://github.com/szimek/signature_pad) library to capture smooth, natural-looking signatures and pass them back to FileMaker as Base64 encoded PNG data.

## Features

- **Responsive Design**: Automatically adjusts to the size of the Web Viewer.
- **Premium Aesthetics**: Modern, clean UI with visual feedback and clear action buttons.
- **Bi-directional Integration**: Uses the `FileMaker.PerformScript` function for seamless communication.
- **Smooth Strokes**: High-quality canvas rendering with velocity filtering.
- **No Hosting Required**: The entire HTML/JS bundle can be stored directly within a FileMaker calculation.

## How to use

### 1. FileMaker Setup

Create a script in your FileMaker solution named **`WebDirect_ReceiveSignature`**. This script will receive the signature data from the Web Viewer.

Example FileMaker Script:
```filemaker
# WebDirect_ReceiveSignature
Set Variable [ $dataURL ; Value: Get ( ScriptParameter ) ]
If [ IsEmpty ( $dataURL ) ]
    Exit Script []
End If

# Get just the Base64 data
Set Variable [ $base64Data ; Value: Let ( [
        ~commaPos = Position ( $dataURL ; "," ; 1 ; 1 )
    ] ;
        Middle ( $dataURL ; ~commaPos + 1 ; Length ( $dataURL ) )
    ) ]

# Store in Container
Set Field [ YourTable::SignatureContainer ; Base64Decode ( $base64Data ; "signature.png" ) ]

Commit Records/Requests [ With dialog: Off ]
```

### 2. Layout Integration

1.  Add a text object off your layout and name it `signature_html`.
2.  Add a **Web Viewer** to your layout.
3.  Set the Web Viewer calculation:
    ```filemaker
    Case ( 
        PatternCount ( Get ( ApplicationVersion ) ; "Web" ) ;
        "data:text/html;base64," & Base64Encode ( GetLayoutObjectAttribute ( "signature_html" ; "content" ) ) ;
        "data:text/html," & GetLayoutObjectAttribute ( "signature_html" ; "content" )
    )
    ```
4.  **Crucial**: Check the option **"Allow JavaScript to perform FileMaker scripts"** in the Web Viewer setup.

#### How this works: 

The webiewer grabs the contents of the `signature_html` text object and displays it in the web viewer using the `data:text/html` protocol. The `Base64Encode` function is used to encode the HTML content as a Base64 string, which is then decoded by the web viewer.

**TIP**

> Since this uses a CDN for the signature_pad library, the user's browser will need internet access. If the environment is offline, you can embed the library's minified code directly into the HTML string.

## Files

- `signature_html.html`: The core integration file containing HTML, CSS, and JavaScript.

## Attribution

- [Signature Pad](https://github.com/szimek/signature_pad) by Szymon Nowak.
- [jsDelivr](https://www.jsdelivr.com/) for CDN hosting of requirements.

## License

MIT
