<script>
    import { Button } from "sveltestrap";
    import {
        Collapse,
        Navbar,
        NavbarToggler,
        NavbarBrand,
        Nav,
        NavItem,
        NavLink,
        Dropdown,
        DropdownToggle,
        DropdownMenu,
        DropdownItem,
    } from "sveltestrap";
</script>

<body>
    <Navbar color="light" light expand="md">
        <NavbarBrand href="#/">CO2ding</NavbarBrand>
        <Nav class="ms-auto" navbar>
            <NavItem>
                <NavLink href="#/distribution">Distribution</NavLink>
            </NavItem>
            <NavItem>
                <NavLink href="#/summary">Summary</NavLink>
            </NavItem>
        </Nav>
    </Navbar>
</body>
