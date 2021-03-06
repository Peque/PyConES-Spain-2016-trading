% DEVELOPING A REAL-TIME AUTOMATED TRADING PLATFORM WITH PYTHON
% Miguel Sánchez de León Peque
% 2016-10-08

# About

## Us

- [OpenSistemas](http://www.opensistemas.com)
- [www.opensistemas.com](http://www.opensistemas.com)
- R&D division
- [Fernando Monera Daroqui](https://www.linkedin.com/in/monera)
  fmonera@opensistemas.com
- [Guillermo Alonso Núñez](https://www.linkedin.com/in/guillermo-alonso-núñez-806423123)
  galonso@opensistemas.com

## Me

- [Miguel Sánchez de León Peque](https://www.linkedin.com/in/peque)
  msdeleon@opensistemas.com
- **Industrial engineer**
- Passion for **programming, data and ML**
- Met **Python** about 2 years ago

# Motivation

## Search engine results

- TradeStation
- Metatrader
- NinjaTrader
- Many others...

## Cons

- Proprietary
- Heavily desktop oriented
- Linux support?
- Can I use Python to create my strategies?

---

![](./figures/cry.gif){width=60%}

## What we wanted

- Control
- Optional GUI
- Multiplatform
- Python

----

> Is this possible?

---

![](./figures/dramatic-pause.gif){width=80%}

# osMarkets

## Basics

- **Broker-independent** platform
- Implemented **over osBrain**
- Designed for **real-time automated trading**

## Overview

![](./figures/architecture.svg){width=100%}

## Feeder

- Get market data
- Multithread

## Overview

![](./figures/architecture.svg){width=100%}

## Router

- Broker independent provider
- Stores/distributes
- Update/resample

## Market data

- Tick
- Bar
- Bar Series

## Bar series

![](./figures/barseries.svg){width=100%}

## Overview

![](./figures/architecture.svg){width=100%}

## Brain

- Subscriptions
- Run algorithms
- Send orders
- Great ecosystem
- Can do anything

## Abstraction

![](./figures/brain-hierarchy-grayscale.svg){width=60%}

## Overview

![](./figures/architecture.svg){width=100%}

## Trader

- Handle orders
- Multithread

## Example

![](./figures/architecture_2.svg){width=100%}

## Other agents

- Logger
- Informer
- Overmind

# GUI

## Features

- Real-time **market data visualization**
- Real-time **indicators visualization**
- **Integration** with osMarkets

## Qt

- Great **portability**
- Probably the **most widely used**
- LGPL

## PyQtGraph

- **Pure Python** graphics library
- **Fast** real-time display
- User **interaction**
- MIT

## Overview

- [Main Window](./video/gui_main_window.webm)
- [Chart](./video/gui_chart.webm)
- [Indicators](./video/gui_indicators.webm)

## Note

![](./figures/architecture_gui.svg){width=80%}

# osBrain

## Basics

> A **general purpose** multi-agent system

- Independent agents
- Message passing
- Easy configuration and deployment

## Other use cases

![](./figures/portfolio_osbrain.svg){width=100%}

## Processes vs. threads

Have you met GIL?

## ØMQ

> ØMQ is very cool -
> If you don't have a project that needs it... ¡create one!

## A basic agent

- Is a system **process**
- Implements methods for easy **binding** and **connecting** with different
  **patterns**
- Activates on **incoming message**
- Mulithreading may be used (*inproc* socket)

## Configuration

> Agents are independent, but they must know the addresses of other agents


- There may be **many sockets**!
- For most agents, assigning a **random address** is simpler

## If only we could...

- Have a **name server**
- Have an **Overmind**...

## Pyro4

- **PY**thon **R**emote **O**bjects
- Treat remote objects as local
- Already has a name server implementation

## A real agent

- Is a system **process**
- This process runs a **Pyro multiplexed server**
- The server serves an actual **Agent object**
- Main thread runs the **main loop** (one-way)
<!--
- Other threads allow for **concurrent access for configuration** (*c = 2*)

## Safe calls

```python
def example(self, x):
    self.x = x

def example_safe(self, x):
    self.call_safe('example', x)
```
-->

## Conclusion

- General-purpose multi-agent system with Python
- Independent agents (processes)
- Message passing using ØMQ
- Easy deployment and remote configuration using Pyro4

# Code examples

## An example (I)

```python
import time
from osbrain import random_nameserver
from osbrain import run_agent


def log_message(agent, message):
    agent.log_info('received: %s' % message)


if __name__ == '__main__':

    # System deployment
    ns = random_nameserver()
    sender = run_agent('Alice', ns)
    receiver = run_agent('Bob', ns)

    # System configuration
    addr = sender.bind('PUSH', alias='main')
    receiver.connect(addr, handler=log_message)

    # Send messages
    while True:
        time.sleep(1)
        sender.send('main', 'Hello, world!')
```


## An example (II)

```python
from osmarkets.brain import Brain
from osmarkets.architecture import OandaArchitecture


class Example(Brain):
    def on_new_bar(self, series):
        # Algorithm
        if series[0].close > series[1].close:
            side = 'buy'
        else:
            side = 'sell'
        # Order
        self.send_order(side=side,
                        size=100,
                        symbol=series.bsid.symbol)


if __name__ == '__main__':

    system = OandaArchitecture(logging=True,
                               account_id=123456,
                               access_token='123abc-345def',
                               environment='live')
    system.stream(['EUR_USD'])

    system.add_brain('example', base=Example)
    system['example'].set_attr(DEBUG=True)
    system['example'].subscribe(('EUR_USD', 1, 'Minutes'), 100)
```

# Final conclusion

----

![](./figures/rejoice.gif){width=80%}

# One last thing...

----

![](./figures/osbrain-logo-name.svg){width=15cm}

- [https://pypi.python.org/pypi/osbrain](https://pypi.python.org/pypi/osbrain)
- [https://github.com/opensistemas-hub/osbrain](https://github.com/opensistemas-hub/osbrain)
- [https://osbrain.readthedocs.io/](https://osbrain.readthedocs.io/)

---

![](./figures/come.gif){width=80%}

# The end

## Contact

- [Miguel Sánchez de León Peque](https://www.linkedin.com/in/peque)
  *msdeleon@opensistemas.com*
- [Fernando Monera Daroqui](https://www.linkedin.com/in/monera)
*fmonera@opensistemas.com*
- [www.opensistemas.com](www.opensistemas.com)
- [robintradinghub.com](http://robintradinghub.com/)
- [blog.opensistemas.com](http://blog.opensistemas.com/)
- [twitter.com/opensistemas](https://twitter.com/opensistemas)

## We are hiring!

- [http://www.opensistemas.com/what-we-do/](http://www.opensistemas.com/what-we-do/)

```python
hired = [name for name, CV in candidates
         if CV in inbox('rrhh@opensistemas.com') and CV]
```

## Thank you!

- Questions?
- Comments?
- Suggestions?
