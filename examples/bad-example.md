# Bad Example

This is what weak, low-value documentation looks like, the kind this skill
is meant to avoid.

```python
def calculate_discount(price, user):
    # calculates the discount
    if user.is_premium:
        return price * 0.8
    if user.purchase_count > 10:
        return price * 0.9
    return price
```

## Why this is weak
- The comment just restates the function name, it adds zero information
- No parameter types or descriptions
- No explanation of the two different discount rules or why they exist
- No mention of what happens if `price` is negative or `user` is `None`
- No return type or return value explanation
