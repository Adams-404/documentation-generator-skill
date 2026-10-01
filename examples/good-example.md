# Good Example

The same function, documented the way this skill expects.

```python
def calculate_discount(price: float, user: User) -> float:
    """
    Calculate the discounted price for a user based on their account status.

    Premium users always receive a 20% discount. Non-premium users with
    more than 10 completed purchases receive a 10% loyalty discount.
    All other users pay full price.

    Args:
        price (float): The original price before discount. Must be >= 0.
        user (User): The user object. Must have `is_premium` (bool) and
            `purchase_count` (int) attributes.

    Returns:
        float: The final price after any applicable discount is applied.

    Raises:
        ValueError: If `price` is negative.

    Example:
        >>> calculate_discount(100.0, premium_user)
        80.0
    """
    if price < 0:
        raise ValueError("price cannot be negative")

    if user.is_premium:
        return price * 0.8
    if user.purchase_count > 10:
        return price * 0.9
    return price
```

## Why this is strong
- States exactly which users get which discount, and why
- Documents types and constraints for both parameters
- Explains the return value clearly
- Documents the error case (`ValueError` on negative price)
- Includes one short, concrete usage example
