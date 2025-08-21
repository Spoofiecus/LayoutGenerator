# Documentation for the Advanced Sticker Layout Generator

## 1. Overview

The `AdvancedStickerLayout.gsm` is a powerful script for Corel Draw designed to automate the process of laying out stickers for production. It takes a selected shape, determines the most cost-effective way to arrange it on a 650mm wide roll, and generates a full production-ready layout and a price quote.

Key features include:
-   Automatic, cost-based orientation selection (Horizontal vs. Vertical).
-   User-prompt for ambiguous layout choices.
-   Flexible bleed handling.
-   Automatic completion of partial rows.
-   Layouts optimized for cutting order (top-left start).
-   Automatic grouping of production batches.
-   Detailed price quote generation.

## 2. How to Use

1.  **Open your document** in Corel Draw.
2.  **Create or import the sticker shape** you want to lay out.
3.  **Size the shape correctly** based on whether the job includes bleed (see section 3.1 below).
4.  **Select the single sticker shape.** You must have one shape selected for the script to work.
5.  **Run the `CreateAdvancedLayout` macro.**
6.  **Answer the two prompts:**
    -   **"Does this job have bleed? (1 for Yes, 0 for No)"**: Enter `1` if your selected shape's dimensions already include the bleed. Enter `0` if you have selected the cutline size.
    -   **"Enter Total Number of Stickers Needed"**: Enter the minimum number of stickers the customer has ordered. The script will automatically round this up to complete the final row.
7.  **Choose Orientation (if prompted):** If the price for laying out the stickers horizontally or vertically is nearly identical, a prompt will appear. It will show you the number of stickers per row and the price for both options. Enter `1` for Horizontal or `2` for Vertical to make the final decision.
8.  **Review the Layout and Quote:** The script will then automatically create the full layout on your page and display a final message box containing the detailed price quote.

## 3. Calculation Logic Explained

### 3.1. Bleed and Sizing Logic

The script's logic is controlled by the first prompt: **"Does this job have bleed?"**. Your answer determines how the script interprets the size of the shape you have selected.

-   **If you answer `1` (Yes, has bleed):**
    -   You should select a shape whose dimensions **include the bleed**. (e.g., for a 40x40mm sticker with 0.5mm bleed on all sides, you should select a **41x41mm** shape).
    -   **Layout:** The script will use the shape's size as-is (41x41mm) for the layout, with no extra gap.
    -   **Pricing:** The script will subtract 1mm from the dimensions to get the cutline size (40x40mm) before calculating the price.

-   **If you answer `0` (No bleed):**
    -   You should select a shape that is the **exact cutline size** (e.g., 40x40mm).
    -   **Layout:** The script will add a 1mm safety gap to your shape's dimensions (making the layout footprint 41x41mm) to ensure the cutlines do not touch.
    -   **Pricing:** The script will use the shape's size as-is (40x40mm) for the price calculation.

### 3.2. Stickers per Row Calculation

The number of stickers that can fit in a row is calculated based on the physical space each sticker takes up on the 650mm wide roll.

-   `LayoutWidth` = The sticker's width for a given orientation, plus a 1mm gap if it's a no-bleed job.
-   `StickersPerRow` = `650 / LayoutWidth`
-   The result is **always rounded down** to the nearest whole number to ensure the layout never exceeds the media width.

### 3.3. Pricing Formula (The "Allocated Width" Model)

The script uses a pricing model based on the proportional area of the roll that each sticker consumes. This ensures that the cost accurately reflects material usage, including any waste between stickers in a row.

The formula for the price of a single sticker is:
`Price = ((AllocatedWidth * CutlineHeight) / 1,000,000) * VinylCost`

Where:
-   `StickersPerRow` is calculated as described above.
-   `AllocatedWidth` = `650 / StickersPerRow`. This gives each sticker an equal slice of the 650mm roll width.
-   `CutlineHeight` = The height of the sticker for the chosen orientation (without bleed/gap).
-   `VinylCost` = The cost per square meter of the vinyl (default is 460.0).

The script calculates this price for both the horizontal and vertical orientations.

### 3.4. Optimal Orientation Choice

The script automatically determines the most cost-effective orientation to use.

1.  It calculates the final `Price` per sticker for both the horizontal and vertical layouts.
2.  It compares the two prices. If one is clearly cheaper (by more than R0.01), it is chosen automatically.
3.  If the prices are nearly identical, a prompt appears, allowing you to make the final decision.

## 4. Layout Features

### 4.1. Reversed Cutting Order

To ensure the cutting process starts at the **top-left** of the layout, the script creates the stickers in reverse order. It calculates the position of all stickers first, and then creates the bottom-right-most sticker first, and the top-left-most sticker last.

### 4.2. Automatic Batch Grouping

The layout is created in "batches" that are 650mm wide and up to 350mm tall. For better organization in the final Corel Draw document, all stickers created within a single batch are automatically **grouped** together. This makes it easy to select, move, or manage an entire batch at once.
