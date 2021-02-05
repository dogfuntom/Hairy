<script>
  import P5 from './PiFiveAction';
  export let sketch = null;
  export let engine = null;

  let holder = null;

  export function restart() {
    engine.remove();
    engine = P5(holder, sketch);
  }

  /**
  * @param {HTMLElement} node
  */
  function foo(node, workspace) {
    holder = node;
    engine = P5(node, workspace);
    return engine;
  }
</script>

<figure use:foo={sketch} />

<style>
  figure {
    display: inline;
    margin: 0;
  }
</style>