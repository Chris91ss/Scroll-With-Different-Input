# Scroll With Different Input

## Overview
This Unity script allows for scrolling through UI elements using various input devices (gamepad, keyboard, etc.) rather than relying on the mouse scroll wheel. It is particularly useful for creating UI menus that can be navigated with game controllers or keyboard inputs.

## Features
- Scroll UI elements using gamepad or keyboard input.
- Flexible implementation for any scrollable content (e.g., dropdowns).
- Automatically adjusts the scroll position based on the selected item.
- Works with TextMeshPro Dropdowns or any other UI elements.

## Table of Contents
1. [Getting Started](#getting-started)
2. [How to Use](#how-to-use)
3. [Code Overview](#code-overview)
4. [Customization](#customization)
5. [License](#license)

---

## Getting Started

### Prerequisites
- Unity 2019.4 or later.
- TextMeshPro package (if using TMP dropdowns).

### Installation
1. Clone or download the repository into your Unity project.
   ```bash
   git clone https://github.com/your-username/Scroll-With-Different-Input.git
   ```
2. Drag and drop the `ScrollWithInput.cs` script into the **Template** of the Dropdown or any scrollable UI element.

---

## How to Use

### Step-by-Step Instructions:

1. **Set up the Dropdown:**
   - Create a new **TextMeshPro Dropdown** (or any scrollable UI content).
   - Find the **Template** object under the Dropdown hierarchy.
   
2. **Attach the Script:**
   - Add the `ScrollWithInput` script to the **Template** GameObject of the Dropdown (or any scrollable content GameObject).
   
3. **Configure the Script:**
   - In the **Inspector**, set references for:
     - **Scroll Rect**: The Scroll Rect component of the Dropdown or scrollable content.
     - **Viewport Rect Transform**: The viewport area where the scrollable content is displayed.
     - **Content Rect Transform**: The container that holds the scrollable items.

4. **Test the Inputs:**
   - You should now be able to scroll through the Dropdown options using gamepad or keyboard navigation.

---

## Code Overview

### `ScrollWithInput.cs`

The `ScrollWithInput` script handles the logic for scrolling a `ScrollRect` UI component based on the currently selected item. It works by checking if the selected item is outside the visible viewport and automatically adjusting the scroll position.

#### Main Script Breakdown:

- **Scroll Rect, Viewport, Content References**: These are the core UI components that define the scrollable area.
- **Selected Item Handling**: It checks if the currently selected UI item is inside or outside the visible viewport.
- **Scrolling Logic**: If the selected item is outside the viewport, it calculates how much to scroll, ensuring smooth navigation.

#### Example Code Snippet:
```csharp
void Update()
{
    var selected = EventSystem.current.currentSelectedGameObject;
    if (selected == null || !selected.transform.IsChildOf(contentRectTransform)) return;

    selectedRectTransform = selected.GetComponent<RectTransform>();
    var viewportRect = viewportRectTransform.rect;

    // Calculate scroll based on the position of the selected item
    var selectedRect = selectedRectTransform.rect;
    var selectedRectWorld = selectedRect.Transform(selectedRectTransform);
    var selectedRectViewport = selectedRectWorld.InverseTransform(viewportRectTransform);

    var outsideOnTop = selectedRectViewport.yMax - viewportRect.yMax;
    var outsideOnBottom = viewportRect.yMin - selectedRectViewport.yMin;

    if (outsideOnTop < 0) outsideOnTop = 0;
    if (outsideOnBottom < 0) outsideOnBottom = 0;

    var delta = outsideOnTop > 0 ? outsideOnTop : -outsideOnBottom;

    if (delta == 0) return;

    var contentRect = contentRectTransform.rect;
    var contentRectWorld = contentRect.Transform(contentRectTransform);
    var contentRectViewport = contentRectWorld.InverseTransform(viewportRectTransform);

    var overflow = contentRectViewport.height - viewportRect.height;
    var unitsToNormalized = 1 / overflow;
    scrollRect.verticalNormalizedPosition += delta * unitsToNormalized;
}
```

---

## Customization

You can customize the scrolling behavior by:
- Modifying the scroll speed based on user preferences.
- Adjusting the viewport size or content size if your UI layout changes dynamically.
- Adding support for horizontal scrolling if needed.

---

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for more details.
