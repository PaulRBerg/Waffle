.. _testing:

Basic testing
=============

Creating a provider
-------------------

Creating a mock provider for your tests is super simple.

.. code-block:: ts

  import { MockProvider } from 'ethereum-waffle';
  const provider = new MockProvider();

This class takes an optional :code:`MockProviderOptions` parameter in the constructor. Then the :code:`ganacheOptions` from :code:`MockProviderOptions` are passed to the underlying ganache-core implementation. You can read more `about the options here <https://github.com/trufflesuite/ganache-core#options>`__.

Getting wallets
---------------

To obtain wallets that have been prefunded with eth use the provider

.. code-block:: ts

  import { MockProvider } from 'ethereum-waffle';

  const provider = new MockProvider();
  const [wallet, otherWallet] = provider.getWallets();

  // or use a shorthand

  const [wallet, otherWallet] = new MockProvider().getWallets();

By default this method returns 10 wallets. You can modify the returned wallets, by changing MockProvider configuration.

.. tabs::

  .. group-tab:: Waffle 3.0.0

    .. code-block:: ts

      import { MockProvider } from 'ethereum-waffle';
      const provider = new MockProvider({
          ganacheOptions: {
            accounts: [{balance: 'BALANCE IN WEI', secretKey: 'PRIVATE KEY'}]
          }
      });
      const wallets = provider.getWallets();

  .. group-tab:: Waffle 2.5.0

    .. code-block:: ts

      import { MockProvider } from 'ethereum-waffle';
      const provider = new MockProvider({
        accounts: [{balance: 'BALANCE IN WEI', secretKey: 'PRIVATE KEY'}]
      });
      const wallets = provider.getWallets();

You can also get an empty random wallet by calling:

.. code-block:: ts

  import { MockProvider } from 'ethereum-waffle';
  const provider = new MockProvider();
  const wallet = provider.createEmptyWallet();

Deploying contracts
-------------------

Once you compile your contracts using waffle, you can deploy them in your javascript code. You need to provide three arguments:
  - wallet to send the deploy transaction
  - contract information (abi and bytecode)
  - contract constructor arguments
  
And you can optionally pass a fourth argument:
  - transaction request options (like gas limit)

Deploy a contract:

.. code-block:: ts

  import BasicTokenMock from "build/BasicTokenMock.json";

  token = await deployContract(wallet, BasicTokenMock, [wallet.address, 1000]);

The contract information can be one of the following formats:

.. code-block:: ts

  interface StandardContractJSON {
    abi: any;
    evm: {bytecode: {object: any}};
  }

  interface SimpleContractJSON {
    abi: any[];
    bytecode: string;
  }

Linking
-------

Link a library:

.. code-block:: ts

  myLibrary = await deployContract(wallet, MyLibrary, []);
  link(LibraryConsumer, 'contracts/MyLibrary.sol:MyLibrary', myLibrary.address);
  libraryConsumer = await deployContract(wallet, LibraryConsumer, []);

.. note:: You need to use a fully qualified name as the second parameter of the link function (path to the file relative to the root of the project, followed by a colon and the contract name).
