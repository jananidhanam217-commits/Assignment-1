# ==========================================
# ASSIGNMENT 1 – CO1
# VILLAGE MILK CO-OPERATIVE PAYMENT CALCULATOR
# ==========================================


# TASK 2 – FLOAT AND DECIMAL COMPARISON

from decimal import Decimal
import sys

payments = ["33.40"] * 1000 + ["0.10"] * 3


def total_float(items):
    total = 0.0

    for amount in items:
        total += float(amount)

    return total


def total_decimal(items):
    total = Decimal("0.00")

    for amount in items:
        total += Decimal(amount)

    return total


print("float :", total_float(payments))
print("Decimal:", total_decimal(payments))

print(
    0.1 + 0.2 == 0.3,
    Decimal("0.1") + Decimal("0.2") == Decimal("0.3")
)

print(sys.getsizeof(100))
print(sys.getsizeof(Decimal("33.40")))
print(sys.getsizeof(33.40))


# TASK 3 – VARIABLES AND CONSTANTS

from decimal import Decimal

BASE_RATE = Decimal("30.00")
FAT_STEP_VALUE = Decimal("0.40")
SNF_STEP_VALUE = Decimal("0.30")
STANDARD_FAT = Decimal("3.5")
STANDARD_SNF = Decimal("8.5")
STEP_SIZE = Decimal("0.1")
MIN_FAT = Decimal("2.0")
MAX_FAT = Decimal("7.0")

x = 10
x = "ten"

a = 10
b = 20
a, b = b, a

print(a, b, x)


# TASK 4 – CORRECT EXPRESSIONS

fat = Decimal("4.2")
snf = Decimal("8.7")
litres = Decimal("8.5")

fat_steps = round((fat - STANDARD_FAT) / STEP_SIZE)
snf_steps = round((snf - STANDARD_SNF) / STEP_SIZE)

rate = (
    BASE_RATE
    + fat_steps * FAT_STEP_VALUE
    + snf_steps * SNF_STEP_VALUE
)

rate = rate.quantize(Decimal("0.01"))
amount = (litres * rate).quantize(Decimal("0.01"))

print("Fat steps:", fat_steps)
print("SNF steps:", snf_steps)
print("Rate:", rate)
print("Amount:", amount)


# TASK 5 – READ INPUT AND CONVERT TYPES

from decimal import Decimal, InvalidOperation


def read_decimal(prompt):
    while True:
        text = input(prompt).strip()

        try:
            return Decimal(text)

        except InvalidOperation:
            print("Enter a valid number, for example 8.5.")


litres = read_decimal("Litres: ")
fat = read_decimal("Fat %: ")
snf = read_decimal("SNF %: ")

print(f"{'Litres':>8} {'Fat':>7} {'SNF':>7}")
print(f"{litres:8.2f} {fat:7.2f} {snf:7.2f}")


# TASK 6 – ORGANISE LOGIC INTO FUNCTIONS

from decimal import Decimal

BASE_RATE = Decimal("30.00")
FAT_STEP_VALUE = Decimal("0.40")
SNF_STEP_VALUE = Decimal("0.30")


def rate_per_litre(fat, snf):
    fat_steps = round(
        (fat - Decimal("3.5")) / Decimal("0.1")
    )

    snf_steps = round(
        (snf - Decimal("8.5")) / Decimal("0.1")
    )

    return (
        BASE_RATE
        + fat_steps * FAT_STEP_VALUE
        + snf_steps * SNF_STEP_VALUE
    ).quantize(Decimal("0.01"))


def payout(litres, fat, snf):
    return (
        litres * rate_per_litre(fat, snf)
    ).quantize(Decimal("0.01"))


def ten_day_total(entries):
    total = Decimal("0.00")

    for litres, fat, snf in entries:
        total += payout(litres, fat, snf)

    return total.quantize(Decimal("0.01"))


# Example
entries = [
    (Decimal("10"), Decimal("3.5"), Decimal("8.5")),
    (Decimal("8.5"), Decimal("4.2"), Decimal("8.7")),
    (Decimal("12"), Decimal("3.1"), Decimal("8.3"))
]

print("Ten-day total:", ten_day_total(entries))


# TASK 7 – BUILD A MODULE
# Save this section separately as milkrate.py

from decimal import Decimal

BASE_RATE = Decimal("30.00")
FAT_STEP_VALUE = Decimal("0.40")
SNF_STEP_VALUE = Decimal("0.30")


def rate_per_litre(fat, snf):
    fat_steps = round(
        (fat - Decimal("3.5")) / Decimal("0.1")
    )

    snf_steps = round(
        (snf - Decimal("8.5")) / Decimal("0.1")
    )

    rate = (
        BASE_RATE
        + fat_steps * FAT_STEP_VALUE
        + snf_steps * SNF_STEP_VALUE
    )

    return rate.quantize(Decimal("0.01"))


def payout(litres, fat, snf):
    return (
        litres * rate_per_litre(fat, snf)
    ).quantize(Decimal("0.01"))


if __name__ == "__main__":
    for litres, fat, snf in [
        (Decimal("10"), Decimal("3.5"), Decimal("8.5")),
        (Decimal("8.5"), Decimal("4.2"), Decimal("8.7")),
        (Decimal("12"), Decimal("3.1"), Decimal("8.3"))
    ]:
        rate = rate_per_litre(fat, snf)

        print(
            f"{litres:5} L fat {fat} snf {snf} "
            f"rate {rate:6.2f} "
            f"pay {payout(litres, fat, snf):8.2f}"
        )


# TASK 7 – MODULE IMPORT EXAMPLE
# Save separately as use_milkrate.py

from decimal import Decimal
import milkrate as mr

pay = mr.payout(
    Decimal("8.5"),
    Decimal("4.2"),
    Decimal("8.7")
)

print(pay)


# TASK 9 – FINAL PAYMENT CALCULATOR

from decimal import Decimal, ROUND_HALF_UP

CENT = Decimal("0.01")


def money(value):
    return value.quantize(
        CENT,
        rounding=ROUND_HALF_UP
    )


def calculate_rate(fat, snf):
    fat_steps = (
        (fat - Decimal("3.5")) / Decimal("0.1")
    ).quantize(
        Decimal("1"),
        rounding=ROUND_HALF_UP
    )

    snf_steps = (
        (snf - Decimal("8.5")) / Decimal("0.1")
    ).quantize(
        Decimal("1"),
        rounding=ROUND_HALF_UP
    )

    return money(
        Decimal("30.00")
        + fat_steps * Decimal("0.40")
        + snf_steps * Decimal("0.30")
    )


def calculate_payment(litres, fat, snf):
    if litres <= 0:
        raise ValueError("Quantity must be positive")

    if not Decimal("2.0") <= fat <= Decimal("7.0"):
        raise ValueError("Fat must be between 2.0 and 7.0")

    return money(
        litres * calculate_rate(fat, snf)
    )


def ten_day_total(entries):
    return money(
        sum(
            (calculate_payment(*entry) for entry in entries),
            Decimal("0.00")
        )
    )


if __name__ == "__main__":
    examples = [
        (Decimal("10"), Decimal("3.5"), Decimal("8.5")),
        (Decimal("8.5"), Decimal("4.2"), Decimal("8.7")),
        (Decimal("12"), Decimal("3.1"), Decimal("8.3"))
    ]

    for row in examples:
        print(calculate_payment(*row))


# TASK 9 – AUTOMATED TESTS

from decimal import Decimal

assert calculate_payment(
    Decimal("10"),
    Decimal("3.5"),
    Decimal("8.5")
) == Decimal("300.00")

assert calculate_payment(
    Decimal("8.5"),
    Decimal("4.2"),
    Decimal("8.7")
) == Decimal("283.90")

assert calculate_payment(
    Decimal("12"),
    Decimal("3.1"),
    Decimal("8.3")
) == Decimal("333.60")

print("ALL TESTS PASSED")


# TASK 10 – FLOAT VS DECIMAL EXPERIMENT

from decimal import Decimal, ROUND_HALF_UP
from time import perf_counter


def measure(n):
    values = ["33.40"] * n

    start = perf_counter()
    f_total = sum(float(x) for x in values)
    float_time = perf_counter() - start

    start = perf_counter()
    d_total = sum(
        (Decimal(x) for x in values),
        Decimal("0.00")
    )
    decimal_time = perf_counter() - start

    print(n, f_total, d_total)
    print("Difference:", Decimal(str(f_total)) - d_total)
    print("Seconds:", float_time, decimal_time)


for n in (300, 6000, 150000):
    measure(n)


# TASK 10 – RATE TABLE VS RECALCULATION

import random
import time
from decimal import Decimal

random.seed(1)


def random_pair():
    fat = Decimal(str(round(random.uniform(2, 7), 1)))
    snf = Decimal(str(round(random.uniform(7, 9.5), 1)))

    return fat, snf


pairs = [
    random_pair()
    for _ in range(150000)
]

start = time.perf_counter()

[
    rate_per_litre(fat, snf)
    for fat, snf in pairs
]

calc_time = time.perf_counter() - start

table = {}

start = time.perf_counter()

for pair in pairs:
    if pair not in table:
        table[pair] = rate_per_litre(*pair)

    result = table[pair]

table_time = time.perf_counter() - start

print(
    "Recalculate:",
    calc_time,
    "Dictionary lookup:",
    table_time,
    "Distinct pairs:",
    len(table)
)


# TASK 12 – SEASONAL RATE AND BONUS CALCULATOR

from decimal import Decimal, ROUND_HALF_UP

D = Decimal
CENT = D("0.01")


def rounded_steps(value, standard):
    return (
        (value - standard) / D("0.1")
    ).quantize(
        D("1"),
        rounding=ROUND_HALF_UP
    )


def rate_per_litre(fat, snf, season="regular"):
    if season == "apr_jun":
        base = D("32.00")
        fat_step_value = D("0.45")

    else:
        base = D("30.00")
        fat_step_value = D("0.40")

    fat_steps = rounded_steps(fat, D("3.5"))
    snf_steps = rounded_steps(snf, D("8.5"))

    rate = (
        base
        + fat_steps * fat_step_value
        + snf_steps * D("0.30")
    )

    return rate.quantize(
        CENT,
        rounding=ROUND_HALF_UP
    )


def ten_day_payment(entries, season="regular"):
    subtotal = D("0.00")
    litres_total = D("0.00")

    for litres, fat, snf in entries:
        litres_total += litres

        subtotal += (
            litres * rate_per_litre(fat, snf, season)
        )

    subtotal = subtotal.quantize(
        CENT,
        rounding=ROUND_HALF_UP
    )

    bonus = D("0.00")

    if litres_total > D("500"):
        bonus = (
            litres_total * D("0.50")
        ).quantize(
            CENT,
            rounding=ROUND_HALF_UP
        )

    return (
        subtotal,
        bonus,
        (subtotal + bonus).quantize(CENT)
    )


# Example
sample_entries = [
    (D("300"), D("4.23"), D("8.67")),
    (D("201"), D("4.23"), D("8.67"))
]

print(
    ten_day_payment(
        sample_entries,
        season="apr_jun"
    )
)
